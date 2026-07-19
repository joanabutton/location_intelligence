# Roadmap

## MVP status (2026-07-19)

The project now has a complete, end-to-end pipeline: `Notebooks/04_opportunity_score_and_recommendation.ipynb`
takes the seven variables produced by Phases 1-3 (§3.2 only) through Phases 5-8 in a single notebook,
producing a real `opportunity_score` per BGRI subsection, a sensitivity analysis, choropleth/interactive
maps, and a first recommendation. See that notebook for the full methodology and findings.

This was a deliberate scope decision: rather than continuing to add variables before ever answering
the project's actual question, the phases below marked **Deferred to v2** were consciously parked so
that a full pipeline could ship first. They remain the clearest, most valuable next steps — each one
strengthens an index that Notebook 04 currently has to approximate with fewer inputs than planned:

- Phase 2.3 (property value proxy) — would sharpen the Affluence index, currently `pct_university` +
  `pct_foreign_nationals` only.
- Phase 3.1 (immediate family public realm), 3.3 (accessibility), 3.4 (cultural amenities) — would
  give the Urban Quality index more than its current single input (`dist_large_park_entrance_m`).
- Phase 4 (competition & risk) — needed for the ground-truth validation step (§7.3) that Notebook 04
  explicitly could not do without a real café dataset.

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

### 2.3 Property Value Proxy - Deferred to v2


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

The café depends on people wanting to spend time somewhere. Implemented in `Notebooks/03_explore_public_realm_lingering_potential.ipynb`, organised into subsections §3.1–§3.4 to match the notebook directly (the original 3-topic split below has been folded into this single phase).

### 3.1 Immediate family public realm (400m) - Deferred to v2

Small parks and gardens, plus playgrounds as a supporting indicator. Planned in the notebook
(`family_public_realm_score`) but not yet coded or accumulated into the master layer.

Questions:

Which neighbourhoods have access to quality public space?
Which areas support leisure-oriented pedestrian activity?
Which dense residential areas also have family-oriented public space?

### 3.2 Destination leisure area (400m)

Large parks and gardens.

Originally planned as a simple 400m buffer around each park. The notebook now anchors this on park entrances instead, with a 400m walking-distance network service area rather than a Euclidean buffer around the whole park polygon — see the data dictionary's "Park Entrances & Destination Leisure Area" section for the full rationale.

### 3.3 Accessibility - Deferred to v2

Public transport
Metro stations
Bus stops
Walkability
Street network
Connectivity
Pedestrian environment

This directly supports the "easy local habit" concept.

### 3.4 Cultural and intellectual amenities (600m) - Deferred to v2

Neighbourhood character — comes directly from the project brief's emphasis on local identity and everyday life.

Candidate datasets
Libraries
Bookshops
Cultural venues
Independent retail
Community facilities

Questions:

Which areas feel like neighbourhood centres rather than dormitory districts?

## Phase 4 - Deferred to v2
Competition & Risk

Only after understanding demand. Also the prerequisite for the ground-truth validation step in
Phase 7.3 below, which Notebook 04 explicitly skipped for lack of this data rather than
approximating it from memory.

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


# MVP synthesis (Phases 5-8): Completed

Implemented as a single notebook, `Notebooks/04_opportunity_score_and_recommendation.ipynb`, run
against the seven variables available at the time (Phases 1, 2, and 3 §3.2 only) rather than
waiting for the deferred phases above. The sub-sections below describe the original plan; see the
notebook itself for what was actually run and its numeric findings.

## Phase 5. Data preparation for modelling

This is the part many beginners skip, but it is where professional analysts spend huge amounts of time.

### 5.1 Explore distributions

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

### 5.2 Correlation analysis

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

### 5.3 Variable selection

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

## Phase 6. Standardisation

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

### 6.1 Build thematic indices

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

### 6.2 Weighting strategy

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

**Implemented as-is:** Notebook 04 uses exactly this 35/25/25/15 split (Demand 35%, Family 25%,
Urban Quality 25%, Affluence 15%), with each index built from whichever of the seven available
variables fit it (Urban Quality currently has only one input, `dist_large_park_entrance_m`,
pending the deferred Phase 3 sub-phases above).

## Phase 7. Opportunity score calculation

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

### 7.1 Sensitivity analysis

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

**Implemented as:** two scenarios rather than three - Family-focused and Premium-focused. The
Remote Worker Café scenario was dropped for this MVP pass because its natural inputs (coworking
spaces, cafe-friendly amenity mix) are Phase 3.4 variables that don't exist yet; adding that
scenario is a natural extension once Phase 3.4 lands. Findings: the Family-focused scenario tracks
the baseline closely (Spearman 0.97, 62% top-decile overlap); the Premium-focused scenario diverges
much more (Spearman 0.79, 43% overlap), showing that a meaningful share of the baseline's
recommendation rests on demand/family/urban-quality strength rather than pure affluence.

### 7.2 Spatial visualisation

Produce:

Choropleth opportunity map
Hotspot map
Top decile map
Candidate locations map

Possibly:

Top 5%
Top 10%
Top 25%

**Implemented as:** static choropleth (quantiles, k=5) and top-decile maps in the notebook, plus an
interactive Folium map with baseline/Family-focused/Premium-focused layers, exported to
`Maps/opportunity_score_map.html`.

### 7.3 Ground truth validation

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

**Not done in the MVP pass:** this step needs the Phase 4 café dataset, which doesn't exist yet.
Notebook 04 explicitly declined to approximate it from memory rather than pass off a guess as
validation - see Phase 4 above.

## Phase 8. Final recommendation

The project ultimately should answer:

If I had €150,000 and wanted to open this café tomorrow, where should I start looking?

**Implemented as:** a top-15 subsection table with sub-index breakdown and centroid coordinates,
grouped into four coordinate-based clusters (no verified freguesia-name join exists yet - see the
notebook's caveat). The two strongest starting points identified: the cluster bordering Porto's
largest urban park (`DTMNFR21` code `131216`, highest single score in the city at 66.4/100, leaning
on family/urban-quality strength), and a riverside cluster (`131217`) with the only genuinely high
Affluence scores in the top 15 - the clearer "moderate premium" candidate. A quick win for v2: join
`DTMNFR21` to an official CAOP or OSM administrative-boundary layer to replace these codes with
real parish names before treating this as a site-visit shortlist.

Not:

Porto has many interesting neighbourhoods.

Something like:

Western Aldoar
Southern Bonfim
Central Foz do Douro

with explanation of trade-offs.