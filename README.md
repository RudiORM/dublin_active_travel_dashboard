# Dublin Region Active Travel Dashboard 

## Contact
* Rudi O'Reilly Meehan, [Data&Design](https://www.dataanddesign.ie/)
* Jack Kavanagh, Open Data Lead, [Smart Dublin](jack.kavanagh@dublincity.ie)

## Table of Contents
1. [Overview](#overview)
2. [Data Sources](#data-sources)
3. [Features](#features)
4. [Technical Implementation](#technical-implementation)
5. [Setup and Deployment](#setup)
6. [Data Processing](#data-processing)

## Overview <a name="overview"></a>
The Dublin Active Travel Dashboard is a comprehensive visualization platform that combines multiple data sources to provide insights into walking and cycling patterns across Dublin. The dashboard helps stakeholders understand active travel trends and their impacts on health and environment through interactive visualizations and real-time data.

## Data Sources <a name="data-sources"></a>

### 1. Census Travel Analysis Dataset
[CSO Census data Commuting to Work Census of Population 2022 Profile 7 - Employment, Occupations and Commuting - Central Statistics Office](https://www.cso.ie/en/releasesandpublications/ep/p-cpp7/censusofpopulation2022profile7-employmentoccupationsandcommuting/commutingtowork/#:~:text=Commuting%20Trends%20Over%20Time,%2C%20up%208%25%20since%202016)
* Generated through _Calculations/Census_calculations/census calculations.ipynb_ which processes:
* 2016-2022 Small Area census statistics 
* Geographic boundaries for Small Areas and Electoral Divisions
* Comparative analysis of transport modes including:
   * Walking and cycling patterns
   * Public transport usage
   * Private vehicle usage
* Outputs census_data_total.geojson for visualization

### 2. Google Modal Split Dataset
[Dublin - Summary - Google Environmental Insights Explorer - Make Informed Decisions](https://insights.sustainability.google/places/ChIJL6wn6oAOZ0gRoHExl6nHAAo)
* Generated through _Calculations/Census_calculations/google_calculations
.ipynb_ which processes:
* Constituency-level transportation data
* Mode split calculations across Dublin
* Geographic boundary processing 
* Outputs districts.geojson with consolidated metrics

### 3. Eco-Visio Counter Data
[Pedestrian and Cycle counter API for Dublin Region - Dataset - data.smartdublin.ie](https://data.smartdublin.ie/dataset/pedestrian-and-cycle-counter-api-for-dublin-region)
* Real-time pedestrian and cycling counter information
* Accessed via Eco-Visio API
* Provides multiple temporal views:
   * Hourly (last 30 days)
   * Weekly (last 3 months)
   * Monthly (last 3 years)

### 4. Strava Metro Dataset
* Developed by Smart Dublin
* Extrapolates total bicycle traffic volumes
* Coverage of 5 key Dublin routes (2021-2023)

## Features <a name="features"></a>
* Interactive map visualization
* Real-time counter data display 
* Historical trend analysis
* Health impact calculations:
   * Premature deaths averted
   * CO2 emissions saved
   * Congestion time avoided
   * Fuel cost savings
* Multiple temporal views
* District-level aggregation

## Technical Implementation <a name="technical-implementation"></a>
* Frontend: Svelte/SvelteKit
* Data Visualization: D3.js
* Mapping: MapBox
* API Integration: Eco-Visio API

## Setup and Deployment <a name="setup"></a>

* Available at [this link](https://active-travel-dublin.vercel.app/)

* Install your own version by using the following
```bash
# Install dependencies
npm install

# Configure environment variables
ECOVISIO_API_KEY=your_api_key
PUBLIC_MAPBOX_TOKEN=your_mapbox_token

# Start development server
npm run dev
