# Population Density

| Variable          | Source      | Description           |
| ----------------- | ----------- | --------------------- |
| N_INDIVIDUOS      | Census 2021 | Resident population   |
| N_INDIVIDUOS_0_14 | Census 2021 | Children aged 0–14    |
| pop_density       | Derived     | Residents per hectare |
| child_density     | Derived     | Children per hectare  |


Source:
Instituto Nacional de Estatística (INE)

Platform:
Sistema de Informação Geográfica dos Censos 2021

Dataset:
BGRI 2021 (Base Geográfica de Referenciação de Informação)

Geographic Level:
Subsecção Estatística (BGRI 2021)

Municipality:
1312 - Porto


# Educational Attainment

| Variable       | Source      | Description                                         |
| -------------- | ----------- | ---------------------------------------------------- |
| pct_university | Derived     | % of residents (N_INDIVIDUOS) holding a university degree (N_INDIVIDUO_ENSINCOMP_SUP) |

Source:
Instituto Nacional de Estatística (INE)

Platform:
Sistema de Informação Geográfica dos Censos 2021

Dataset:
FS 2021 Secção Tot (Resultados à Secção Estatística, Censos 2021)

Geographic Level:
Secção Estatística (joined to BGRI 2021 subsections via SECCAO, derived from DTMNFR21 + SECNUM21)

Municipality:
1312 - Porto

Notes:
3 sections in the FS 2021 Secção Tot file have no SECCAO code (municipality/freguesia subtotal rows) and are excluded before joining to the geometry.


# Family Household Concentration

| Variable                            | Source  | Description                                              |
| ------------------------------------ | ------- | --------------------------------------------------------- |
| family_households_per_100_residents | Derived | Family nuclei with children under 25 (N_NUCLEOS_FAMILIARES_COM_FILHOS_TENDO_O_MAIS_NOVO_MENOS_DE_25), per 100 residents (N_INDIVIDUOS) |

Source:
Instituto Nacional de Estatística (INE)

Platform:
Sistema de Informação Geográfica dos Censos 2021

Dataset:
BGRI 2021 (Base Geográfica de Referenciação de Informação)

Geographic Level:
Subsecção Estatística (BGRI 2021)

Municipality:
1312 - Porto


# Foreign National Residents

| Variable               | Source  | Description                                                          |
| ----------------------- | ------- | ---------------------------------------------------------------------- |
| pct_foreign_nationals   | Derived | % of residents (N_INDIVIDUOS) with foreign nationality (N_INDIVIDUOS_NAC_ESTRANGEIRA) |

Source:
Instituto Nacional de Estatística (INE)

Platform:
Sistema de Informação Geográfica dos Censos 2021

Dataset:
FS 2021 Secção Tot (Resultados à Secção Estatística, Censos 2021)

Geographic Level:
Secção Estatística (joined to BGRI 2021 subsections via SECCAO)

Municipality:
1312 - Porto


# Private Schools (Rede Escolar)

| Variable                    | Source  | Description                                                                 |
| ---------------------------- | ------- | ----------------------------------------------------------------------------- |
| NOME                         | Rede Escolar | School name                                                              |
| MORADA                       | Rede Escolar | Street address                                                           |
| CICLO                        | Rede Escolar | Education cycle(s) offered (e.g. Pré-escolar, 1º/2º/3º Ciclo, Secundário, Profissional, Artistico, Especial) |
| NATUREZAINSTITUCIONAL_DESC   | Rede Escolar | Institutional nature: "Particular" or "IPSS ou equiparada"               |
| geometry                     | Rede Escolar | Point location (EPSG:3763, reprojected to EPSG:4326 for mapping)         |
| school_category              | Derived | "private_preschool_or_nursery" or "other_private_education", assigned from CICLO |

Source:
Rede Escolar (Ministério da Educação)

Dataset file:
RedeEscolar_mapa_-8499650067991149947.geojson

Geographic Level:
Point location (school)

Municipality:
Porto (CONCELHO = "Porto")

Processing notes:
- Filtered to NATUREZAINSTITUCIONAL_DESC == "Particular" (private schools), then to family-relevant CICLO values (excludes Profissional/Artistico/Especial-only schools) to produce `family_schools`.
- Exported to `../Data/processed/family_private_schools.geojson` (53 schools total, 50 with usable point geometry, sorted by NOME).
- Known data quality issue: 3 schools have missing geometry (Colégio De Nossa Senhora Da Esperança, Jardim De Infância Do Viso – A.S.A.S., Jardim Escola João De Deus Porto). Excluded from `family_schools_mappable` and everything derived from it (maps, catchment buffers, the accessibility indicator below) until geocoded.

Derived variables/layers:

| Variable / Layer                | Source  | Description |
| -------------------------------- | ------- | ----------- |
| private_schools_access_400m      | Derived | Per-BGRI indicator: count of `family_schools_mappable` schools whose point falls within a 400m buffer of the BGRI centroid. Accumulated into `bgri_master.gpkg` (own_col of notebook 02). 400m radius chosen after testing multiple distances, informed by local knowledge of typical school-to-café walking patronage in Porto. |
| private_school_buffers_400m/600m/800m | Derived | 400/600/800m buffers around each school **point** in `family_schools_mappable`, for QGIS visual catchment inspection only — not an analytical variable. Exported to `Data/Processed/private_school_buffers.gpkg`. Distinct from `private_schools_access_400m` above, which buffers BGRI centroids rather than schools. |


# Public Realm and Lingering Potential

| Variable / Layer              | Source           | Description |
| ----------------------------- | ---------------- | ----------- |
| playgrounds_osm               | OpenStreetMap via OSMnx | Playgrounds in Porto, downloaded using OSM tag `leisure=playground` |
| parks_gardens_osm             | OpenStreetMap via OSMnx | Parks and gardens in Porto, downloaded using OSM tags `leisure=park` and `leisure=garden` |
| small_parks_gardens           | Derived from OpenStreetMap | Parks/gardens with calculated area below 20,000 m2 |
| large_parks_gardens           | Derived from OpenStreetMap | Parks/gardens with calculated area greater than or equal to 20,000 m2 |
| playgrounds_400m_buffer       | Derived | 400m buffer around playground features, for QGIS inspection and future accessibility scoring |
| small_parks_400m_buffer       | Derived | 400m buffer around small park/garden features, for immediate family public realm potential |
| large_parks_400m_buffer       | Derived | 400m buffer around large park/garden features, for destination leisure area potential |
| area_m2                       | Derived | Feature area in square metres, calculated after reprojecting OSM geometries to EPSG:3763 |
| geometry_type                 | Derived | Geometry type retained for QGIS inspection |
| porto_walk_network            | OpenStreetMap via OSMnx | Porto's pedestrian street/path network (edges), downloaded with `ox.graph_from_polygon(porto.geometry.iloc[0], network_type="walk")`. Used as the basis for network-based (non-Euclidean) park-entrance service areas in §3.2 — see below. |

Source:
OpenStreetMap

Access method:
OSMnx Python library, using `ox.geocode_to_gdf("Porto, Portugal")` to obtain the Porto boundary and `ox.features_from_polygon()` to download features inside that boundary.

Notebook:
`Notebooks/03_explore_public_realm_lingering_potential.ipynb`

Exported files:
- `../Data/Processed/playgrounds_osm.gpkg`
- `../Data/Processed/parks_osm.gpkg`
- `../Data/Processed/urban_amenities.gpkg`

Layers in `urban_amenities.gpkg`:
- `porto_boundary`
- `playgrounds_osm`
- `parks_gardens_osm`
- `small_parks_gardens`
- `large_parks_gardens`
- `playgrounds_400m_buffer`
- `small_parks_400m_buffer`
- `large_parks_400m_buffer`
- `porto_walk_network`

Geographic Level:
OpenStreetMap feature geometry clipped/downloaded within Porto municipality boundary.

Coordinate Reference System:
Downloaded from OpenStreetMap in WGS84, then reprojected to EPSG:3763 for metric area and buffer calculations.

Processing notes:
- OSM data was downloaded directly in Notebook 3 using OSMnx, not from a separate local source file.
- The public realm variables are currently exploratory QGIS layers; final census-subsection accessibility indicators still need to be derived by spatial join/intersection with the BGRI geography.
- Park/garden size classes use a 20,000 m2 threshold: `< 20000` for small parks/gardens and `>= 20000` for large parks/gardens.

`porto_walk_network` processing notes (§3.2 — see `park_entrances_draft.gpkg` below for how it's used):
- Fetched with OSMnx's `network_type="walk"`, which already excludes motorways/trunk roads and other non-pedestrian classes via its built-in filter.
- A defensive check additionally removes any edge tagged `highway` in `{motorway, motorway_link, trunk, trunk_link}` that slipped through, then reports how many (if any) were removed.
- Downloaded in WGS84 (OSMnx's native CRS), then reprojected to EPSG:3763 with `ox.project_graph()` so downstream distance/buffer calculations are in metres.
- Exported as edges only (`u`, `v`, `key`, `highway`, `length`, `geometry`) via `ox.graph_to_gdfs(walk_graph_proj, nodes=False, edges=True)` — network nodes are not exported as a separate layer.

## Park Entrances & Destination Leisure Area (§3.2)

`large_parks_access_400m` (the destination-leisure indicator named in §3.2) is anchored on park **entrances**, not on a buffer around each park's whole polygon boundary — a boundary buffer overstates access by treating every metre of edge as equally reachable (including edges facing a river, a walled-off backyard, or a road with no crossing) and by measuring straight-line distance instead of the distance someone actually has to walk. See the rationale at the top of §3.2 in `Notebooks/03_explore_public_realm_lingering_potential.ipynb` for the full argument.

| Variable | Source | Description |
| -------- | ------ | ----------- |
| dist_large_park_entrance_m | Derived | Per-BGRI indicator: **network walking distance (m) to the nearest large-park entrance** — multi-source Dijkstra from all 133 entrances over the corrected §3.2.4 routing graph; each subsection reads the value at the reachable network node nearest its centroid. Continuous and threshold-free: any cutoff view (e.g. ≤400m) or decay transform is derivable later. Chosen over an entrance-count variant, which mostly measured how many gates a park had (a digitisation artifact) and was 68% zeros with a hard 400m cliff. Accumulated into `bgri_master.gpkg` (own_col of notebook 03, §3.2.7 — that notebook's first master contribution). Porto distribution: min 29m, median 823m, max 2,894m. |

| Variable / Layer                      | Source | Description |
| -------------------------------------- | ------ | ----------- |
| entrances_review                       | Manual (QGIS) | Candidate park entrances — OSM footpath × park-boundary crossings, reviewed point-by-point in QGIS, judged uncertain/needing a second look. Ground-truthed against parks including Covelo, Queimódromo and Palácio de Cristal. **Not reproducible by code** — a fixed manual input. |
| entrances_kept                         | Manual (QGIS) | Same review process as `entrances_review`, but confirmed as usable entrances. **Not reproducible by code.** |
| entrances_excluded                     | Manual (QGIS) | Candidates rejected during the same review (e.g. not a real/usable entrance). **Not reproducible by code.** Excluded from all layers below. |
| entrances                              | Derived | `entrances_review` + `entrances_kept` combined into one layer (`entrance_id`, `source`, `park` columns). Code-generated (notebook 03 §3.2.1) from the two manual inputs above — reproducible *given* those inputs, but ultimately depends on them. |
| entrances_400m_buffer_euclidean        | Derived | Plain 400m circular buffer around each `entrances` point. Straight-line baseline only, kept for comparison against the network service area — not the final accessibility measure. |
| entrances_400m_service_area_network    | Derived | 400m **walking-distance** service area per entrance, following `porto_walk_network` rather than a Euclidean circle: snap each entrance to the nearest network node, take the sub-network reachable within 400m of cumulative edge length (`networkx.ego_graph`, weighted by `length`), buffer the reachable centrelines by a small corridor half-width, and dissolve. This is the "network buffer" approximation to a true isochrone. Entrances with no reachable edges within range fall back to a small buffer around the snapped node. |

Source:
`entrances_review`/`entrances_kept`/`entrances_excluded` — manual digitisation/review in QGIS. Everything else — derived in code from those three inputs plus `porto_walk_network`.

Notebook:
`Notebooks/03_explore_public_realm_lingering_potential.ipynb`, §3.2.1–§3.2.6

Exported file:
`Data/Processed/park_entrances_draft.gpkg` — layers `entrances_review`, `entrances_kept`, `entrances_excluded`, `entrances`, `entrances_400m_buffer_euclidean`, `entrances_400m_service_area_network`.

Coordinate Reference System:
EPSG:3763 throughout — the manual review layers were already digitised in this CRS, so no reprojection is needed for the derived layers built on top of them.

Processing notes:
- `entrances_review`/`entrances_kept`/`entrances_excluded` predate any notebook code — no script produces them, so they are excluded from the "fully reproducible by rerunning the notebooks" guarantee that applies to the rest of this project's outputs. Losing `park_entrances_draft.gpkg` without a backup would lose this manual review work permanently.
- `entrances_400m_buffer_euclidean` vs. `large_parks_400m_buffer` (in the section above): both are Euclidean, but one buffers entrance **points** and the other buffers whole park **polygons** — they are not the same variable, and only the entrance-based ones feed the current §3.2 methodology.
- Validation of the network service area against both Euclidean baselines (visual map + numeric area comparison) lives in notebook 03 §3.2.6.
- **The routing graph behind `entrances_400m_service_area_network` needs four corrections before it produces sensible reach** (full detail in notebook 03 §3.2.4): (1) walk-network edges lying entirely inside a `large_parks_gardens` polygon are excluded, so the 400m budget can't be absorbed looping around a park's own interior trails (found at Parque do Covelo); (2) entrances snap to the nearest node of that *filtered* graph, not the full one; (3) nodes within 15m are consolidated, bridging OSM's footpath-vs-street digitization gaps at what is really the same junction; (4) each entrance additionally connects, non-destructively, to both endpoints of every real street edge within 30m — some entrance connectors were never topologically joined to the street they sit beside (found at Covelo's Rua do Bolama entrance and five entrances sharing one street at Parque Oriental).
- **Known residual limitations** (all 133 entrances audited): a few entrances legitimately can't reach some nearby-looking streets within 400m of walking — walled estates (Serralves), escarpments (Palácio de Cristal), river-valley detours (Parque Oriental / Rio Tinto), and one Av. da Boavista stub at Parque da Cidade with no pedestrian crossing mapped in OSM. These reflect real barriers or upstream OSM gaps, not routing errors.


# Opportunity Score (MVP Synthesis)

Synthesises the seven variables above (Phases 1, 2, and 3 §3.2) into thematic indices and a
weighted opportunity score per BGRI subsection. Computed only for subsections with
`N_INDIVIDUOS > 0` (1,609 of 1,659) — the 50 zero-population subsections (riverbanks, the port,
motorway segments, Parque da Cidade itself) get `NaN` for every variable below rather than a
misleading zero, since a location-suitability score has no meaning where there is no resident
population.

| Variable | Source | Description |
| -------- | ------ | ----------- |
| demand_index | Derived | Min-max scaled `pop_density` (0-1). |
| family_index | Derived | Mean of min-max scaled `child_density`, `family_households_per_100_residents`, and `private_schools_access_400m` (0-1). |
| urban_quality_index | Derived | Min-max scaled `dist_large_park_entrance_m`, inverted (`1 - scaled`) so that higher is better, matching the other indices. |
| affluence_index | Derived | Mean of min-max scaled `pct_university` and `pct_foreign_nationals` (0-1). |
| opportunity_score | Derived | `100 × (0.35×demand_index + 0.25×family_index + 0.25×urban_quality_index + 0.15×affluence_index)`. Baseline "everyday neighbourhood cafe" weighting. Porto distribution (scored sample, n=1,609): min 6.7, median 33.7, mean 33.5, max 66.4. |
| opportunity_score_family_focused | Derived | Same formula, alternate weights (Demand 25%, Family 45%, Urban Quality 20%, Affluence 10%). Spearman rank correlation with `opportunity_score`: 0.966. |
| opportunity_score_premium_focused | Derived | Same formula, alternate weights (Demand 25%, Family 15%, Urban Quality 20%, Affluence 40%). Spearman rank correlation with `opportunity_score`: 0.789. |

Min-max scaling is fit on the scored (residential) sample only, so the excluded zero-population
subsections can't distort the observed range.

Source:
Derived entirely from `bgri_master.gpkg` columns produced by Notebooks 01-03 — no new raw data.

Notebook:
`Notebooks/04_opportunity_score_and_recommendation.ipynb`

Exported files:
- `Data/Processed/04_bgri_opportunity_score.gpkg` — full scored sample (1,609 rows) with all intermediate scaled columns and indices.
- `Maps/opportunity_score_map.html` — interactive Folium map, baseline/family-focused/premium-focused layers.

Processing notes:
- `own_cols` for this notebook: `demand_index`, `family_index`, `urban_quality_index`, `affluence_index`, `opportunity_score`, `opportunity_score_family_focused`, `opportunity_score_premium_focused`. Standard drop-then-merge accumulation into `bgri_master.gpkg`; the 50 excluded subsections are merged back in with these columns as `NaN` so the master's row count (1,659) is unaffected.
- Correlation check (Pearson/Spearman) across the seven source variables before indexing found no pair redundant enough to drop outright; the strongest was `pop_density`/`child_density` at 0.81/0.84, which sit in different indices (Demand vs. Family) rather than the same one.
- `pct_foreign_nationals` is flagged in the notebook as the weakest-justified input in Affluence — it could equally reflect tourism-adjacent turnover (a risk factor per the concept-to-variable framework) rather than "cosmopolitan but local." Revisit once Phase 4 tourism-intensity data exists.
- Ground-truth validation against existing cafe locations (roadmap Phase 7.3) was explicitly not attempted — it requires the Phase 4 competition dataset, which doesn't exist yet, and approximating it from memory would not be a real validation.
- Final recommendation clusters are described by coordinate and `DTMNFR21` code, not by parish name — the BGRI source carries no freguesia-name field, and no administrative-boundary join has been done yet (flagged as a v2 quick win in the notebook and in `Documents/04_roadmap.md`).
