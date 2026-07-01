# Location Intelligence — Retail Site Selection for Porto

A data-driven methodology for identifying optimal locations for an independent, family-oriented neighbourhood café in Porto, Portugal. Built as a portfolio case study demonstrating geospatial, statistical, and urban reasoning skills.

**Status:** Ongoing portfolio project. Phases 1-2 are complete; Phase 3 public-realm data extraction and QGIS export is underway.

## Project Overview

**Business question:** Which locations in Porto offer the strongest opportunity for an independent family-friendly neighbourhood café?

This project does not predict revenue. It builds a **location suitability model** that ranks areas by their fit with a specific café concept: specialty coffee, homemade pastries, a children's corner, a calm atmosphere, and a regular local clientele. The target customer is a local resident with moderate-to-high purchasing power — parents, freelancers, and culture-oriented locals who value familiarity and quality over speed or cost.

The full methodology is documented in [`Documents/01_project_brief.md`](Documents/01_project_brief.md).

## Repository Structure

```
location_intelligence/
├── Documents/          Project brief, analytical framework, data sources, roadmap, data dictionary, glossary
├── Notebooks/          Jupyter notebooks — one per analytical phase
├── Data/
│   ├── Raw/            Source data (gitignored; see Data Sources below)
│   └── Processed/      Enriched GeoPackages and GeoJSON outputs
├── Maps/               Static PNG maps and interactive Folium HTML maps
└── QGIS/               QGIS project file for desktop GIS work
```

## Analytical Phases

| Phase | Topic | Status | Notebook |
|-------|-------|--------|----------|
| 1 | Residential Demand — population density, child density, family concentration | Complete | `Notebooks/01_explore_residential_demand.ipynb` |
| 2 | Lifestyle & Purchasing Power — educational attainment, foreign nationals, private schools | Complete | `Notebooks/02_explore_lifestyle_purchasing_power.ipynb` |
| 3 | Public Realm & Lingering Potential — parks, gardens, playgrounds, amenity buffers | In progress | `Notebooks/03_explore_public_realm_lingering_potential.ipynb` |
| 4 | Neighbourhood Character — libraries, bookshops, cultural venues, independent retail | Planned | — |
| 5 | Accessibility — public transport, metro, walkability, street network | Planned | — |
| 6 | Tourism & Competition | Planned | — |
| 7 | Data Preparation — distribution analysis, correlation analysis, variable selection | Planned | — |
| 8 | Standardisation — min-max scaling | Planned | — |
| 9 | Thematic Indices — Family, Affluence, Urban Quality, Daily Demand composites | Planned | — |
| 10 | Opportunity Score — weighted combination, sensitivity analysis, spatial output, ground-truth validation | Planned | — |

The concept-to-variable framework (how qualitative café requirements map to measurable indicators) is in [`Documents/02_concept_to_variables.md`](Documents/02_concept_to_variables.md).

## Key Outputs

- **Master geospatial layer:** [`Data/Processed/bgri_master.gpkg`](Data/Processed/bgri_master.gpkg) — 1,659 Porto census subsections (BGRI 2021) enriched with computed variables from completed census-based phases
- **Interactive combined map:** [`Maps/combined_map.html`](Maps/combined_map.html) — multi-layer Folium map with population density, child density, family concentration, and private schools
- **Private school map:** [`Maps/family_schools_map.html`](Maps/family_schools_map.html) — 50 family-relevant private schools in Porto
- **Public-realm amenity GeoPackage:** [`Data/Processed/urban_amenities.gpkg`](Data/Processed/urban_amenities.gpkg) — QGIS-ready OpenStreetMap layers for Porto boundary, playgrounds, parks/gardens, small and large park subsets, and 400m/800m visual buffer layers
- **OpenStreetMap amenity extracts:** [`Data/Processed/playgrounds_osm.gpkg`](Data/Processed/playgrounds_osm.gpkg) and [`Data/Processed/parks_osm.gpkg`](Data/Processed/parks_osm.gpkg) — source playground and park/garden features exported from Notebook 3
- **Static figures:** `Maps/Figure 1 Relative Population Density...png` and `Maps/Figure 2 Relative Children Density...png`
- **QGIS project:** [`QGIS/location_intelligence.qgz`](QGIS/location_intelligence.qgz) — loads `bgri_master.gpkg` for desktop GIS exploration

## Data Sources

Raw data files are gitignored because they can be reproduced from public sources. Only `bgri_master.gpkg` (the enriched output) is tracked.

| Dataset | Source | File |
|---------|--------|------|
| BGRI 2021 census boundaries + demographics, Porto (code 1312) | [INE](https://www.ine.pt) — Census 2021 | `Data/Raw/BGRI2021_1312.gpkg` |
| Census 2021 results at section level (education, nationality) | [INE](https://www.ine.pt) — Census 2021 | `Data/Raw/FS 2021 Secção Tot.xlsx` |
| Census 2021 results at subsection level | [INE](https://www.ine.pt) — Census 2021 | `Data/Raw/FS 2021 SubSecção Tot.xlsx` |
| Census variable reference guide | [INE](https://www.ine.pt) | `Data/Raw/C2021_FSINTESE_VARIAVEIS.csv` |
| School network (Rede Escolar) | Ministério da Educação / Porto Open Data | `Data/Raw/RedeEscolar_mapa_*.geojson` |
| Parks, gardens, and playgrounds | [OpenStreetMap](https://www.openstreetmap.org) via OSMnx | Downloaded in Notebook 3 and exported to `Data/Processed/urban_amenities.gpkg` |

The primary spatial unit throughout is the **BGRI subsection** (subsecção estatística) — the smallest Portuguese census geography, typically corresponding to a single urban block. Porto has 1,659 of them.

## Environment & Dependencies

The project uses a conda environment named `urban-intelligence-lab`. There is no `requirements.txt` yet; the core libraries are:

```
geopandas>=1.1.3
pandas
numpy
scipy
scikit-learn
matplotlib
seaborn
contextily
mapclassify
folium
rasterio
osmnx
jupyter
```

To reproduce the analysis, download the raw data files from the sources listed above, place them in `Data/Raw/`, and run the notebooks in order.

## Project Documents

| File | Purpose |
|------|---------|
| [`Documents/01_project_brief.md`](Documents/01_project_brief.md) | Concept definition, target customer, analytical objective, success criteria |
| [`Documents/02_concept_to_variables.md`](Documents/02_concept_to_variables.md) | Maps qualitative café requirements to measurable indicators and proxy variables |
| [`Documents/03_identifying_available_datasets.md`](Documents/03_identifying_available_datasets.md) | Available data sources for each indicator |
| [`Documents/04_roadmap.md`](Documents/04_roadmap.md) | Phase-by-phase implementation plan |
| [`Documents/05_data_dictionary.md`](Documents/05_data_dictionary.md) | Field definitions, sources, and computed variable formulas |
| [`Documents/06_glossary.md`](Documents/06_glossary.md) | GIS and analytical terminology reference |

## Coordinate System

All spatial data uses **EPSG:3763** (ETRS89 / Portugal TM06), the official Portuguese projected coordinate system. Interactive maps reproject to Web Mercator (EPSG:3857 / EPSG:4326) for Folium rendering.
