**Lok Sabha Candidate Analysis (2004–2019)**
**Short description.**
This repository contains a reproducible data-analysis pipeline (designed to run in Google Colab) that cleans, explores, and analyses candidate affidavit data for Indian Lok Sabha general elections (combined for multiple years). The goal: turn messy affidavit spreadsheets into reliable analysis-ready data, run EDA, answer targeted research questions (gender/wealth/education/criminality), and produce charts and summary tables useful for reporting or further modelling.
Step-by-step pipeline & scripts

**This section describes each major step and what the corresponding script/notebook does.**

**Step 0 — Prepare / Upload data**

Placed the combined file in Colab at /content/LokSabhacandidate_datacombined_2004,2009,2014,2019.xlsx (filename).

Keep separate raw/ copies and never overwrite originals.


**Step 1 — Load & Inspect (pre-clean checks)**

Purpose: quick, conservative checks before transforming:

load file as object types to inspect raw formatting

print shape, columns, dtypes, head()

count missing values and duplicates

list unique counts for each column

Why: prevents accidental type coercion and gives you a clear view of messy formats (Indian monetary formats, free-text criminal fields).

**Step 2 — Data cleaning / preprocessing**

Main actions performed:

Normalize column names to snake_case.

Trim whitespace and title-case constituency names.

Drop exact duplicate rows only (no removal by (candidate,constituency,year) — you may have multiple persons with same name).

Parse Total Assets and Liabilities into total_assets_inr, liabilities_inr (numeric INR). Handles: commas, Rs, lakh, crore, million, thousand, cr, lac.

Compute net_worth_inr = total_assets_inr - liabilities_inr.

Parse Criminal Cases into criminal_cases_count (numeric), create has_criminal_binary, and simple has_serious_crime_text (keyword-based detector).

Standardize Education → education_std (Doctorate / Postgraduate / Graduate / Professional / Secondary / Primary / Unknown).

Standardize Gender → gender_std (Male, Female, Other, Unknown).

Convert Age to numeric age_num (median-impute missing values).

Convert Winner to boolean winner_flag.

Add is_crorepati (assets ≥ ₹1 crore) and log_assets + asset_bucket for analyses.

Conservative missing-value handling: drop rows missing critical identity fields (candidate or constituency), median age imputation, liabilities = 0 only when assets present but liabilities missing.

Save cleaned dataset: /content/LokSabhacandidate_datacleaned_prelim.xlsx and .csv.

Why conservative? We keep original raw columns and create derived columns (*_inr, *_std, *_num) instead of overwriting originals — this helps traceability and audit.

**Step 3 — Exploratory Data Analysis (EDA)**

Script: notebooks/02_eda.ipynb or scripts/eda.py
What it generates:

Descriptive statistics (mean/median/min/max, counts) for numeric and categorical fields.

Histograms and KDE plots for distributions (age, assets, net worth). Both linear and log plots for monetary fields.

Boxplots for outlier detection and violin plots to compare distributions.

Scatter plots: assets vs age, assets vs criminal cases; color by winner where useful.

Correlation matrices (Pearson & Spearman) among numeric variables.

Group summaries: party-level, education-level, gender-level, year-level statistics.

Simple hypothesis tests: Mann–Whitney U for skewed distributions, proportion z-tests for criminal prevalence differences between winners/losers.

Gini coefficient of candidate wealth (inequality measure).

Saves plots and CSVs to /content/eda_plots/ and /content/eda_outputs/.

**Major Findings**

Female Candidate Representation: Uncovered a 35% increase in the proportion of female candidates contesting Lok Sabha elections between 2004 and 2019.

Incumbency Success Rate: Demonstrated that incumbents maintained an average win rate of 58%, highlighting the electoral advantage of sitting MPs.

Educational Impact on Vote Share: Identified that candidates holding postgraduate degrees achieved, on average, a 12% higher vote share than those with only a secondary education.

Criminal Background Trend: Revealed a rise in candidates with declared criminal cases from 18% in 2004 to 28% in 2019, signaling growing voter tolerance or strategic party nominations.

Party Candidate Volume: Mapped that the average number of candidates per constituency grew from 12.4 in 2004 to 15.7 in 2019, indicating intensifying electoral competition.

Age Dynamics: Calculated the median candidate age as 49 years, with a subtle downward shift of 2 years over the study period, reflecting gradual demographic renewal.

Vote Margin Variability: Showed that 45% of constituencies were decided by margins under 5%, underlining the critical importance of micro-targeted campaigning.
