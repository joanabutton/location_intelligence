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
- Exported to `../Data/processed/family_private_schools.geojson` (50 schools with geometry, sorted by NOME).
- Known data quality issue: 3 schools have missing geometry (Colégio De Nossa Senhora Da Esperança, Jardim De Infância Do Viso – A.S.A.S., Jardim Escola João De Deus Porto). To be located and added manually in QGIS.


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

Geographic Level:
OpenStreetMap feature geometry clipped/downloaded within Porto municipality boundary.

Coordinate Reference System:
Downloaded from OpenStreetMap in WGS84, then reprojected to EPSG:3763 for metric area and buffer calculations.

Processing notes:
- OSM data was downloaded directly in Notebook 3 using OSMnx, not from a separate local source file.
- The public realm variables are currently exploratory QGIS layers; final census-subsection accessibility indicators still need to be derived by spatial join/intersection with the BGRI geography.
- Park/garden size classes use a 20,000 m2 threshold: `< 20000` for small parks/gardens and `>= 20000` for large parks/gardens.
