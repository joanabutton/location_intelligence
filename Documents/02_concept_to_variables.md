# Concept-to-Variable Framework

## Location Intelligence for Retail Site Selection

## Project Use Case

Independent, family-oriented neighbourhood café in Porto.

The aim is to translate the qualitative café concept into measurable location requirements that can later be tested using geospatial and business data.

## 1. Core Location Requirements

| Café concept need              | Location requirement                      | Measurable indicator                                              | Expected relationship                  |
| ------------------------------ | ----------------------------------------- | ----------------------------------------------------------------- | -------------------------------------- |
| Regular local customers        | Strong residential base                   | Residential population density                                    | Higher is better                       |
| Family-oriented atmosphere     | Presence of families and children         | Share of children, schools, nurseries, playgrounds nearby         | Higher is better                       |
| Everyday repeat visits         | Neighbourhood continuity                  | Residential land use, local services, non-tourist amenities       | Higher is better                       |
| Customers who linger           | Pleasant pedestrian environment           | Walkability, low road dominance, proximity to parks/public spaces | Higher is better                       |
| Moderate premium pricing       | Sufficient spending power                 | Income proxy, property values, education levels, rent values      | Higher is better                       |
| Community café identity        | Strong local centre                       | Mix of shops, schools, services, parks, cultural amenities        | Higher is better                       |
| Child-friendly use             | Access to family routes                   | Proximity to schools, parks, residential streets                  | Higher is better                       |
| Cosmopolitan but local         | Urban vibrancy without tourist dependence | Cultural amenities, independent shops, cafés, bookshops           | Moderate/high is good                  |
| Avoid tourist café positioning | Lower dependence on visitors              | Short-term rentals, hotels, tourist attractions                   | Too high is negative                   |
| Avoid oversaturation           | Room for differentiation                  | Existing café density within walking radius                       | Moderate is good, too high is negative |
| Accessible daily habit         | Easy local access                         | Public transport stops, walking catchment, street connectivity    | Higher is better                       |
| Visible but calm               | Street presence without chaos             | Main street proximity, traffic intensity, pedestrian comfort      | Balanced is best                       |

## 2. First-Version Indicator Set

The first model should use a small, defensible set of indicators.

### Positive indicators

1. **Residential density**

   Measures whether enough people live nearby to support repeat local visits.

2. **Family presence**

   Measured through children population, schools, nurseries, playgrounds, or other family-oriented amenities.

3. **Park and public-space access**

   Measures whether the café sits within everyday family and leisure routes.

4. **Walkability**

   Measures whether customers can comfortably reach the café on foot.

5. **Public transport accessibility**

   Measures whether the café is accessible without relying only on car traffic.

6. **Local purchasing power**

   Uses income or property-value proxies to estimate whether customers can support a slightly premium café offer.

7. **Neighbourhood amenity mix**

   Measures whether the area already has enough daily-life activity to support lingering and repeat visits.

8. **Complementary cultural/lifestyle amenities**

   Includes bookshops, galleries, independent shops, libraries, cultural venues, yoga studios, or similar places that suggest a compatible customer base.

### Negative or penalty indicators

1. **Tourism intensity**

   High tourist accommodation density or proximity to major tourist hotspots may reduce fit with a local neighbourhood café.

2. **Café saturation**

   Too many similar cafés nearby may reduce opportunity, unless the area has very high demand.

3. **Major road dominance**

   Heavy traffic corridors may reduce pedestrian comfort and make the café less suitable for lingering.

4. **Low residential presence**

   Areas with few residents may rely too heavily on transient customers.

5. **Office-only demand**

   Areas dominated by weekday office workers may not support the family-oriented, local-repeat model.

## 3. Recommended Initial Scoring Categories

The opportunity score can begin with six categories:

1. Local residential demand
2. Family and community fit
3. Walkability and everyday accessibility
4. Lifestyle and amenity compatibility
5. Purchasing power
6. Risk and penalty factors

Each category should contain only a small number of variables in the first version.

## 4. Suggested First Model Structure

| Category                      | Example indicators                                                       | Role in model             |
| ----------------------------- | ------------------------------------------------------------------------ | ------------------------- |
| Local residential demand      | Residential density, household density                                   | Core positive driver      |
| Family and community fit      | Schools, nurseries, playgrounds, parks                                   | Core positive driver      |
| Walkability and accessibility | Street connectivity, transport stops, pedestrian access                  | Positive support factor   |
| Lifestyle compatibility       | Bookshops, cultural spaces, independent retail, existing specialty cafés | Positive support factor   |
| Purchasing power              | Income proxy, housing prices, education proxy                            | Positive viability factor |
| Risk factors                  | Tourism intensity, café saturation, traffic corridors                    | Penalty factors           |

## 5. Important Modelling Principle

The model should not assume that every positive variable is always better.

Some indicators may have an ideal range rather than a simple high/low relationship.

For example:

* Café density: too low may mean weak demand; too high may mean saturation.
* Tourism: some visitor presence may help, but too much may undermine the neighbourhood concept.
* Main-street proximity: visibility is useful, but heavy traffic may reduce comfort.
* Purchasing power: too low may weaken viability; too high may indicate expensive rents or an overly exclusive area.

These should be treated as curved or threshold-based variables where possible, rather than simple linear scores.

## 6. Spatial Unit of Analysis

The project should avoid analysing Porto only at parish level, because parishes may be too large and uneven.

Preferred options:

1. Grid cells
   For example, 250m x 250m or 500m x 500m grid cells across Porto.

2. Candidate points
   For example, street intersections, retail parcels, or possible commercial locations.

3. Walking catchments
   For each candidate point, calculate indicators within a 5-minute or 10-minute walking radius.

Recommended first version:

Use a regular grid across Porto, then calculate scores for each cell based on nearby amenities and surrounding population.

## 7. Possible Proxy Variables

Because direct footfall and revenue data may not be available, the project can use proxies.

| Missing ideal data   | Possible proxy                                                                   |
| -------------------- | -------------------------------------------------------------------------------- |
| Footfall             | Public transport stops, amenity density, centrality, street network connectivity |
| Family customers     | Schools, nurseries, playgrounds, children population                             |
| Spending power       | Property values, income by area, education level, rent levels                    |
| Local loyalty        | Residential density, neighbourhood services, low tourist accommodation dominance |
| Café demand          | Existing café density, Google review volume, nearby lifestyle amenities          |
| Pleasant environment | Parks, pedestrian streets, low road dominance, public space proximity            |

## 8. Output of This Stage

At the end of this stage, the project should have:

* A list of location requirements
* A first set of measurable indicators
* A distinction between positive indicators and penalty indicators
* A clear spatial unit of analysis
* A list of required datasets
* A defensible reason for each variable included

## 9. Immediate Next Step

The next step is to identify available datasets for each indicator.

For each proposed variable, check:

* Is there public data available?
* Is it geospatial?
* What is the spatial resolution?
* Is it recent enough?
* Can it be used legally and ethically?
* Does it measure the concept directly, or is it only a proxy?

Only variables with usable data should enter the first version of the model.

## 10. Data families

1. Census Variables

Per census section:

- population density
- child density
- educational attainment
- occupation
- housing

These are polygon attributes.

2. Point Amenities

To be collated in reference_points.csv:
- schools
- parks
- playgrounds
- libraries
- cafés
- metro stations

3. Network / Accessibility Layers
- walking times
- street network
- public transport accessibility