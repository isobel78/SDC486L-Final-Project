# Cinematic ROI and Audience Dynamics
## Financial Efficiency, Genre Fatigue, and Global Film Trends

This project analyzes the [Movies Metadata Cleaned Dataset (1900–2025)](https://www.kaggle.com/datasets/mustafasayed1181/movies-metadata-cleaned-dataset-19002025/), a large-scale collection of over 946,000 films sourced from [The Movie Database (TMDB)](https://www.themoviedb.org/). Using variables such as budget, revenue, genres, countries, languages, popularity, and audience ratings, the study investigates how production budgets translate into financial returns, what film attributes drive audience reception, how profitability and runtime have shifted across decades and regions, and whether genre saturation suppresses audience ratings over time.


## Research Questions

1. Once we adjust for inflation, do small indie films, mid-sized movies, or massive blockbusters make the most profit relative to their cost? Is there a spending limit?
2. How do attributes like genre, runtime, language, and studio tier affect how many people watch a film and how much they like it?
3. How have runtime and inflation-adjusted profits changed across the decades and between different countries?
4. Does releasing too many films of the same genre cause audience ratings to decline — and can this effect be measured?


## Dataset

* Movies Metadata Cleaned Dataset (1900–2025) | [Kaggle](https://www.kaggle.com/datasets/mustafasayed1181/movies-metadata-cleaned-dataset-19002025/)
* CPI data (1997–2025) | [US Bureau of Labor Statistics](https://www.bls.gov/cpi/) — `cu.data.0.Current` flat file (https://download.bls.gov/pub/time.series/cu/)
* Historical CPI (pre-1997) | [Federal Reserve Bank of Minneapolis](https://www.minneapolisfed.org/about-us/monetary-policy/inflation-calculator/consumer-price-index-1800-)

The raw dataset contains 946,460 records. After preprocessing, the main analysis uses **365,032 films** (1930–2025). The scenario analysis notebook filters further to **88,352 US feature films released since 2000**.

Place the dataset files in a `dataset/` directory at the project root:

```
dataset/
├── data_movies_clean.xlsx
└── cpi_1913.csv
└── cu.data.0.Current
```


## Dependencies

Both notebooks require **Python 3.9+** and the following packages:

```
pandas
numpy
matplotlib
seaborn
scipy
scikit-learn
openpyxl
IPython
```


## Run the notebooks in order:
* Start with `EDA-and-Models.ipynb` — this notebook performs all preprocessing and trains the models.
* Then run `Scenario-Analysis.ipynb` — this notebook re-trains the Q1 model on a filtered dataset (US films, 2000–2025) and runs the scenario analysis.


## Key Findings

### Q1 — ROI by Budget Tier

Films were divided into three equal-sized budget tiers (Indie / Mid / Blockbuster) at the 33rd and 67th percentiles of log-adjusted budget. A **Gradient Boosting Classifier** predicts which ROI tier a film falls into, and a **Gradient Boosting Regressor** predicts continuous ROI. Both models use inflation-adjusted budget, popularity, vote count, vote average, runtime, and release year as features.

**The top feature importance by far is `log_budget_adj`**, confirming that inflation-adjusted spending is the dominant predictor of ROI. A budget sweep analysis shows ROI collapsing sharply past approximately $10M, demonstrating clear diminishing returns. The median modern film — regardless of tier — produces negative ROI, making budget efficiency the key lever studios can control.

### Q2 — What Drives Popularity and Ratings?

Two separate regression pipelines predict **log-popularity** (audience reach) and **vote_average** (audience quality perception) using only pre-release attributes: genre flags, runtime buckets, language, studio tier, log budget, and release year.

### Q3 — Temporal and Geographic Trends

This section is purely exploratory, visualizing how median runtime, budget, revenue, profit, and ROI have evolved across decades (1930–2025) and across the top 10 film-producing countries.

### Q4 — Does Genre Fatigue Exist?

A panel dataset was constructed at the genre–year level, with lagged volume features (prior-year film count and 3-year rolling average) used to predict next-year average audience ratings. A **chronological train/test split** (pre-2015 training, 2015+ testing) was used to prevent data leakage.

### Scenario Analysis

Using the Q1 Gradient Boosting Regressor retrained on US films released 2000–2025, three real-world filmmaking scenarios are evaluated against a **baseline** (median mid-budget film, 2015 release, predicted ROI: **−0.413**):

| Scenario | Key Assumptions | Predicted ROI | Δ vs Baseline |
|---|---|---|---|
| **Baseline** | Median features, 2015 release | −0.420 | — |
| **S1: Streaming Era Indie** | 20th percentile budget, 75th percentile popularity & engagement, vote avg 7.0, 2024 release | **+0.946** | +1.366 |
| **S2: Peak Blockbuster** | 95th percentile budget, moderate engagement, vote avg 6.5, 2010 release | −0.012 | +0.408 |
| **S3: Genre Fatigue** | Median budget, vote avg 5.8 (69th percentile), 2022 release | −0.255 | +0.165 |

**Key takeaway:** Only the Streaming Era Indie scenario achieves meaningfully positive ROI. S2's near-break-even result despite a massive budget directly confirms the spending limit hypothesis. S3 is especially striking: a genuinely above-average film (69th percentile rating) still predicts negative ROI under genre saturation conditions, and a budget sweep of S3 features shows that a **vote average of at least 8.56** — placing a film in the top 5% of all rated films — is required just to break even under genre fatigue conditions. Budget efficiency and audience reach, not raw spending, are the primary drivers of financial success in modern cinema.

