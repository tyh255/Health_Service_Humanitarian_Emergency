# Health_Service_Humanitarian_Emergency
Replication code for the article, "Health Service Delivery During Humanitarian Emergencies: Quantifying the Impact of Forcibly Displaced Populations, Armed Conflict, and Natural Disasters"

Project Overview

The objective of this analysis is to quantify how various humanitarian stressors affect vaccination systems. It handles data ingestion from the UCDP API, merges multi-source panel data (World Bank, EM-DAT, and Vaccination records), and executes econometric modeling.

1. Displacement Shock Analysis (DiD): 
This module identifies "shocks" in population displacement based on both relative growth and absolute population thresholds.
Methodology: Staggered Difference-in-Differences with a 3-year treatment window.
Key Function: run_did_analysis()
  Features:
Dynamic shock detection based on customizable growth_threshold and absolute_threshold.
Interaction terms to evaluate how conflict intensity (None, Low, High) moderates the impact of displacement.
Event study preparation to validate the parallel trends assumption.

2. UCDP Data Pipeline: 
Automates the retrieval of conflict data directly from the Uppsala Conflict Data Program (UCDP) API.
Endpoints: State-based battle deaths, non-state conflict, and one-sided violence.
  Processing:
Proportional allocation: Deaths in multi-country conflicts are split across participating Gleditsch-Ward (GW) country codes to prevent double-counting.
Automated pagination and retry logic for API stability.

3. Non-Linear Dose-Response (GAMs): 
Uses Generalized Additive Models to move beyond binary "shocks" and explore the continuous impact of stressors.
Model Specification: Coverage ~ s(Stressors) + Fixed Effects + Controls
Stressors Modeled: * Log-transformed total displacement.
Displacement as a percentage of national population.
Natural disaster fatalities.
Battle-related deaths (BRDs).
Visualization: Generates unified dose-response curves with 95% confidence intervals, standardized to relative exposure (0–100%) for cross-stressor comparison.

Required Libraries
R
library(dplyr)      # Data manipulation
library(fixest)     # High-dimensional fixed effects (DiD)
library(mgcv)       # Generalized Additive Models
library(ggplot2)    # Visualization
library(httr)       # API requests
library(jsonlite)   # JSON parsing
library(broom)      # Tidy model outputs
library(purrr)      # Functional programming
library(scales)     # Formatting plot axes
Data Requirements

The scripts expect a merged_data or vaccination_descriptive dataframe containing:
Identifiers: Country.Code (ISO3 or GW), Year.
Outcomes: Coverage (Vaccination percentage).
Predictors: Total (Displaced persons), GDP_per_capita.
Factors: Category (Conflict intensity), vaccine (Type of vaccine).

How to Run: 
Initialize Data: Ensure your base vaccination and population dataframes are loaded.
Execute API Fetch: Run get_ucdp_all_country_year() to pull current conflict data.
Run DiD: Use run_did_analysis() to test for immediate coverage drops following major displacement events.
Fit GAMs: Execute the spline-based models to visualize how increasing levels of disaster or conflict intensity incrementally degrade health systems.
Visualize: Use the provided ggplot2 blocks to generate unified comparative plots.

All analyses are conducted in R (version 4.12) via rStudio. Note that the dose response modeling can take 10-30 minutes for each model to compile. 
