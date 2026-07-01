# Roadmap

## Phase 1 (Completed)
Local Residential Demand

✔ Population density

✔ Child density

Questions answered:

Where do people live?
Where are families concentrated?
How different are the patterns?

At this stage child density remains exploratory rather than a core suitability indicator.

## Phase 2 (Completed)
Lifestyle & Purchasing Power Indicators

### 2.1. actual socioeconomic profile of residents in INE

I would investigate whether Census 2021 provides, at subsection level or another small-area geography:

Educational attainment
% with university degree
% with secondary education
Occupation categories
managers
professionals
technicians
Employment status
Housing tenure
owner occupied
rented
Building characteristics
Household composition

### 2.2. Private Schools

They reveal:

higher purchasing power
education-oriented households
professional households
lifestyles compatible with specialty cafés

And they also represent potential clients that can go there with children.

Analysis:

Map locations
Distance to nearest private school
Count within 500m / 800m / 1000m
Compare with population density

Questions:

Are private schools clustered?
Do they coincide with dense residential neighbourhoods?
Do they identify different neighbourhoods than child density?

### 2.3 Property Value Proxy

This may become more important than schools.

Potential sources:

property sale data
rent data
Idealista datasets if accessible
municipal valuation proxies

Questions:

Which residential areas appear able to support premium pricing?

This directly connects to the concept-to-variable framework's purchasing-power requirement.

## Phase 3
Public Realm & Lingering Potential

The café depends on people wanting to spend time somewhere.

3.1 Parks and Gardens

Questions:

Which neighbourhoods have access to quality public space?
Which areas support leisure-oriented pedestrian activity?

3.2 Playgrounds

Supporting indicator.


Questions:

Which dense residential areas also have family-oriented public space?

## Phase 4

Neighbourhood Character

This comes directly from the project brief's emphasis on local identity and everyday life.

Candidate datasets
Libraries
Bookshops
Cultural venues
Independent retail
Community facilities

Questions:

Which areas feel like neighbourhood centres rather than dormitory districts?

## Phase 5
Accessibility
Public transport
Metro stations
Bus stops
Walkability
Street network
Connectivity
Pedestrian environment

This directly supports the "easy local habit" concept.

## Phase 5 

Tourism

## Phase 6
Competition & Risk

Only after understanding demand.

Existing cafés

Questions:

Is there already a specialty café cluster?
Is there evidence of saturation?
Tourism

Questions:

Which areas are dominated by visitors rather than residents?

This aligns with the brief's warning against tourist-oriented locations.


| Variable               | Best Geography          |
| ---------------------- | ----------------------- |
| Population density     | Subsecção               |
| Children density       | Subsecção               |
| Family density         | Subsecção               |
| Educational attainment | Secção often sufficient |
| Occupation             | Secção often sufficient |
| Private schools        | Point locations         |
| Parks                  | Point/polygon locations |


# After finishing collecting data:

## Phase 7. Data preparation for modelling

This is the part many beginners skip, but it is where professional analysts spend huge amounts of time.

### 7.1 Explore distributions

For each variable:

histogram
mean
median
quartiles
skewness
outliers

Questions:

Is this variable highly skewed?
Are there extreme values?
Should it be transformed?

Example:

private schools within 800m

might be:

0,0,0,0,0,1,1,2,7

which is highly skewed.

### 7.2 Correlation analysis

Create:

Pearson correlation matrix
Spearman correlation matrix

Questions:

Are two variables measuring the same thing?
Are we double-counting?

You may discover:

Variable A	Variable B	Correlation
child density	schools nearby	0.82
graduates	foreigners	0.76

That suggests redundancy.

### 7.3 Variable selection

Decide:

Keep:

population density
children density
graduate %
parks access

Remove:

school density
family density

because they add little additional information.

This stage is sometimes called:

feature engineering and feature selection

## Phase 8. Standardisation

Variables have different units:

Variable	Units
population density	people/km²
graduates	%
parks	count
metro access	distance

These cannot be added directly.

Convert to:

Min-max scaling (my recommendation)

or

z-score normalization

Result:

All variables become:

0 → poor
1 → excellent
Phase 4. Build thematic indices

Instead of 15 independent variables, create themes.

Example:

Family Index
children density
schools accessibility
playground accessibility
Affluence Index
graduates
purchasing power
private schools
Urban Quality Index
parks
culture
walkability
Daily Demand Index
population density
transit accessibility
Phase 5. Weighting strategy

This is where your architecture background becomes surprisingly useful.

Decide:

Dimension	Weight
Demand	35%
Family presence	25%
Urban quality	25%
Affluence	15%

Document your reasoning.

There is no objectively correct answer.

Professional location intelligence models often use expert judgement.

## Phase 9. Opportunity score calculation

Calculate:

Opportunity Score =
0.35 × Demand
+
0.25 × Family
+
0.25 × Urban Quality
+
0.15 × Affluence

Result:

Every BGRI receives:

0 to 100
Phase 7. Sensitivity analysis

This is an unusually sophisticated step for a portfolio project and recruiters love it.

Create multiple scenarios:

Family Café

Heavy family weighting.

Remote Worker Café

Heavy lifestyle weighting.

Premium Café

Heavy affluence weighting.

Compare rankings.

Questions:

Which areas consistently perform well?
Which areas depend heavily on assumptions?
Phase 8. Spatial visualisation

Produce:

Choropleth opportunity map
Hotspot map
Top decile map
Candidate locations map

Possibly:

Top 5%
Top 10%
Top 25%
Phase 9. Ground truth validation

This is arguably the most important stage.

Ask:

Do known successful neighbourhood cafés already exist in high-scoring areas?

If yes:

your model demonstrates external validity.

If no:

you investigate why.

Perhaps:

competition saturation
rent costs
heritage restrictions
tourism distortion

This discussion often becomes one of the strongest sections of a report.

## Phase 10. Final recommendation

The project ultimately should answer:

If I had €150,000 and wanted to open this café tomorrow, where should I start looking?

Not:

Porto has many interesting neighbourhoods.

Something like:

Western Aldoar
Southern Bonfim
Central Foz do Douro

with explanation of trade-offs.