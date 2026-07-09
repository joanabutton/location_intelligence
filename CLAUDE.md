# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A data-driven location intelligence methodology for identifying optimal locations for an independent, family-oriented neighbourhood café in Porto, Portugal. This is a portfolio case study built entirely in Jupyter notebooks — there is no application code, package, or test suite. The full brief is in [`Documents/01_project_brief.md`](Documents/01_project_brief.md); the phase-by-phase plan is in [`Documents/04_roadmap.md`](Documents/04_roadmap.md).

The project scores Porto census subsections (not the whole city uniformly) by how well they fit one specific café concept — it is not a generic "best neighbourhood" ranking. Every new variable must be justified against the concept-to-variable framework in [`Documents/02_concept_to_variables.md`](Documents/02_concept_to_variables.md) before being added.

## Environment

Conda environment named `urban-intelligence-lab`. No `requirements.txt`; core libraries: `geopandas`, `pandas`, `numpy`, `scipy`, `scikit-learn`, `matplotlib`, `seaborn`, `contextily`, `mapclassify`, `folium`, `rasterio`, `osmnx`, `jupyter`.

```
conda activate urban-intelligence-lab
jupyter lab
```

Run notebooks from within `Notebooks/` (they use relative paths like `../Data/Raw/...`) and in numeric order — later notebooks depend on the master layer that earlier notebooks build.

## Architecture: the master-layer accumulation pattern

This is the one piece of cross-file architecture that isn't obvious from any single notebook.

`Data/Processed/bgri_master.gpkg` (layer `"bgri"`) is the **canonical generated analytical layer**: one row per BGRI subsection, keyed by `BGRI2021`, storing derived subsection-level indicators — not raw spatial data. It is the only file under `Data/` tracked in git (see `.gitignore`); everything else in `Data/Raw/` and `Data/Processed/` is reproducible and gitignored. Never hand-edit it; write to it only via the accumulation pattern below.

Each analytical-phase notebook (`Notebooks/0N_*.ipynb`) reads source data, derives its own new columns, and at the end performs **two** exports:

1. A phase-specific GeoPackage (e.g. `Data/Processed/01_bgri_residential.gpkg`) containing the full enriched GeoDataFrame for that phase.
2. An accumulation step onto the master layer: load the existing master if present, drop the columns this notebook owns, merge in the fresh columns keyed on `BGRI2021`, and overwrite the master file. If the master doesn't exist yet, the notebook's own GeoDataFrame becomes the initial master.

The master is **not** appended to indefinitely. On each run a notebook refreshes only its own columns, leaving every other notebook's columns untouched.

### Column ownership

- Every derived variable has exactly **one owner notebook**.
- A notebook may create, update, or remove only the columns listed in its `own_cols` — the drop-then-merge step scopes it to those columns.
- A notebook must **never** overwrite variables owned by another notebook.
- Never recompute or modify a variable created by an earlier notebook unless you are intentionally editing that notebook. Downstream notebooks **consume** upstream indicators (read them from the master, use them in analysis) but do not **redefine** them.

When adding a new phase notebook that contributes census-subsection-level variables, replicate this exact pattern (see the last cell of `Notebooks/01_explore_residential_demand.ipynb` or `Notebooks/02_explore_lifestyle_purchasing_power.ipynb` for the reference implementation) and declare the notebook's `own_cols` explicitly so re-runs don't leave duplicate/stale columns behind.

**Why this exists:** it keeps notebooks independently rerunnable. A methodological change in one notebook can refresh that notebook's variables in the master without rebuilding the whole pipeline or damaging any other notebook's outputs.

### Census indicators vs. raw point/polygon data

Non-census point/polygon data (schools, parks, playgrounds, cafés, OSM amenities — geometry that isn't one row per BGRI subsection) does **not** go into `bgri_master.gpkg`. Keep it as its own raw or enriched layer (e.g. `urban_amenities.gpkg`, `family_private_schools.geojson`). Such a dataset enters the master only **after** being aggregated into per-subsection indicators — counts, distances, densities, or accessibility scores — keyed on `BGRI2021`, at which point those aggregated columns become the owning notebook's `own_cols`. `Notebooks/03_explore_public_realm_lingering_potential.ipynb` rebuilds `urban_amenities.gpkg` from scratch on every run (deletes and recreates the file) since it's a raw OSM extract, not an accumulated layer — don't apply the master-merge pattern there.

## Spatial conventions

- **Primary key**: `BGRI2021` — the census subsection ID. All census-derived variables join on this column.
- **CRS**: all spatial analysis and area/buffer calculations use **EPSG:3763** (ETRS89 / Portugal TM06), the official Portuguese projected CRS. Data arriving in WGS84 (e.g. from OSMnx) must be reprojected to EPSG:3763 with `.to_crs(3763)` before computing area, density, or buffers. Only reproject to EPSG:4326/3857 at the final step, for Folium map rendering.
- **Density units**: densities are expressed per hectare (feature count / (`SHAPE_Area` / 10000)), not per km² or m².
- **Spatial unit**: the BGRI subsection (subsecção estatística) is the unit of analysis throughout — Porto has 1,659 of them. Some indicators (education, occupation) are only available at the coarser Secção Estatística level and are joined down to subsections via a `SECCAO` key derived from `DTMNFR21` + `SECNUM21`.

## Data sources and provenance

Raw inputs live in `Data/Raw/` (gitignored, reproducible from public sources — see the Data Sources table in [`README.md`](README.md)): INE Census 2021 BGRI geometries and section-level results, the Rede Escolar school network extract, and OSM features pulled live via OSMnx inside `Notebooks/03_explore_public_realm_lingering_potential.ipynb` (not from a static file). Every field, its source, and its derivation formula is catalogued in [`Documents/05_data_dictionary.md`](Documents/05_data_dictionary.md) — update it whenever a notebook adds or changes a variable.

## QGIS

`QGIS/location_intelligence.qgz` loads `Data/Processed/bgri_master.gpkg` for desktop GIS work. It has no separate build step; just keep it pointed at the master GeoPackage as new layers/columns are added.

## Notebook conventions

- Notebooks are written as narrative EDA: markdown discussion of a question, a code cell computing/plotting the answer, repeat. Preserve this structure when extending a notebook rather than collapsing steps into helper scripts.
- Static maps use `matplotlib` + `geopandas.plot(scheme="quantiles", k=5, ...)`, often with `contextily.add_basemap` for an OSM basemap underneath. Interactive maps use `folium` and are exported to `Maps/*.html`.
- Numeric columns can contain `inf`/`-inf` from division (e.g. percentage-of-zero denominators); the established pattern is `.replace([float("inf"), -float("inf")], float("nan")).dropna()` before correlation/regression analysis.
