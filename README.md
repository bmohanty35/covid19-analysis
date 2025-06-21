# covid19-analysis

# 1. Problem framing & overall approach
Goal- Explore how a country’s maximum daily COVID‑19 infection rate relates to socio‑economic wellbeing indicators drawn from the 2020 World Happiness Report.
Strategy-
- Build a clean, country‑level COVID‑19 time‑series.

- Derive a single, comparable metric for “how bad did it get here?” (the peak of the first derivative).

- Join that metric to happiness data and look for linear relationships (both visually and with Pearson correlations).

# 2. Data ingestion & cleaning
The COVID-19 dataset (covid19_Confirmed_dataset.csv) is loaded using pandas.read_csv(). From this dataset, unnecessary columns such as latitude, longitude, and province/state names are dropped to retain only the country name and cumulative daily confirmed cases. Since some countries had multiple entries due to regional divisions, the data is grouped by country and the values are summed to obtain a single cumulative time series per country. The resulting cleaned COVID-19 dataset, named corona_dataset_aggregated, contains one row per country and hundreds of columns representing daily counts.

Simultaneously, the World Happiness dataset (worldwide_happiness_report.csv) is also loaded using pandas.read_csv(). Unnecessary columns such as Overall rank, Score, Generosity, and Perceptions of corruption are removed, keeping only those variables relevant to the analysis. The country names in the happiness dataset are set as the index to enable easier joining with the COVID dataset. This results in a cleaned happiness DataFrame containing happiness indicators for each country.


# 3. Feature engineering – “maximum infection rate”
- Take the first derivative: for every country compute daily_new = cumulative.diff() across dates.
- Peak value: max_daily = daily_new.max() gives the single worst day each country experienced.
- Append that scalar as a new column Max_infection_rates.
- Create corona_data – a slim DataFrame with one column (Max_infection_rates) indexed by country.

# 4. Merging the two domains
Inner join corona_data ⟷ cleaned happiness data → data (187 countries retain complete information).

Inspect data.corr() to quantify linear relationships.

(Self‑correlations and cross‑predictor correlations also examined.)

# 5. Exploratory visual analysis
For each happiness variable:

-Scatter‑plots x = predictor, y = log(Max_infection_rates) to linearise the heavy‑tailed infection metric.
- Regression line with sns.regplot() to eyeball slope & fit.

Takeaways (visible in plots):

GDP, Social Support, Healthy Life Expectancy all show positive slopes – wealthier, healthier countries tended to experience slightly higher peak daily case counts (likely a testing/reporting artefact or urban‑density effect). However, freedom score shows weak positive correlation.

# 6. Key findings & interpretation
- Peak spread was only mildly related to wealth or wellbeing. Correlation coefficients in the 0.19–0.29 range indicate that while richer countries on average recorded higher maximum daily infections, the relationship is weak and far from deterministic.

- Freedom of choice had virtually weak linear association with peak infections in early, suggesting personal‑liberty metrics did not much translate directly into worse outbreak control at the pandemic’s onset.

- Methodological caveat: results are sensitive to reporting practices (wealthier countries test more), time window (data appears to stop mid‑2020), absence of population normalisation.

- Actionable insight: controlling for population size, policy response lags, or testing rates in a multivariate model would likely yield more meaningful causal signals than simple bivariate correlations.


# 8. Summary
The notebook performs a clean exploratory data analysis:

1. Data wrangling → 2. Feature creation → 3. Dataset fusion → 4. Correlation/visual diagnostics.

It uncovers associations between early COVID‑19 spread and macro wellbeing indicators.
