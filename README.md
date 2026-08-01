soccer-aging-curves

The goal of this project is to determine at what age soccer players are at their best value using data-science. The project also includes how each soccer position's value changes as they age and determining the differences between each position. Also, the project helps to understand the rate at which players reach their peak value as well as the rate at which each player's value declines. The project also answers the question of whether teams overpay for players that are past their "prime" or peak performance and whether a players financial value matches their performances.

Exploratory Data Analysis Notebook

The purpose of the exploratory data analysis notebook is to use Transfermrkt data imported from Kaggle to analyze every imported player's performance as they age. The datasets imported through Kaggle are separated into various csv's that each individually contribute to understanding the value of players compared to their age. The csv labeled "players" includes many features such as each imported players name, position, and club that they play for. These features are important in identifying players to help make graphs that help to better understand the relationship between a player's value and age. Identifying every player's position can additionally help to understand the second question of how each position's value changes as they age. The csv labeled "appearances" includes features such as each player's goals, assists, and the amount of minutes that they play per season. These features can be helpful in understanding each player's form or value at a certain time. When a player is at their best, they would be scoring and assisting more often than any other year in their career and playing more minutes than any other year in their career. Lastly, the csv labeled "valuations" includes features such as a player's market value and their domestic cup competitions. These can also help to understand how well a player is doing at a given time. A players market value is a quantifiable number to understand the value that they bring to a club and a club doing well in their domestic competition is a good indicator of it's player's current value. 

Using the data imported from Kaggle, it is now time to create graphs that demonstrate the relationship between a player's age and their performances. The first graph represents the relationship between the "raw value" of players with each core position which includes goalies, defenders, midfielders, and attackers. It surprised me to see that defenders had the highest "raw value" because I predicted that the midfielders would have the highest "raw value", followed by the attackers. The appearances vs players graph was also interesting to look at. I instantly noticed the large bar above 0 indicating that most professional soccer players spend the careers without an appearance or with very little appearances. The age distribution and position distribution charts help to understand the spread of players used through the dataset as most players in the dataset are defenders in their 20s.


All analysis is done in **Jupyter notebooks** (pandas + Matplotlib), run
top-to-bottom. See `protocol.md` for how we work and `PLAN.md` for the milestones.

## Project structure

```
soccer-aging-curves/
├── CLAUDE.md         # project charter (role, mission, stack, conventions)
├── PLAN.md           # milestones, dependencies, complexity, risks
├── protocol.md       # working protocol (update README after each commit, etc.)
├── data/
│   ├── raw/          # untouched downloaded data (git-ignored)
│   └── processed/    # cleaned, analysis-ready data (git-ignored)
├── notebooks/        # THE workspace — all analysis, modeling, and figures
├── src/              # small helpers the notebooks import (config, data download)
├── figures/          # exported charts for the paper
└── paper/            # research write-up (outline + drafts)
```

## Getting started

```bash
python -m venv .venv
# Windows PowerShell:
.venv\Scripts\Activate.ps1
pip install -r requirements.txt
```

## Data sources

**Primary:** David Cariboo's [*Player Scores* dataset](https://www.kaggle.com/datasets/davidcariboo/player-scores)
(Transfermarkt) on Kaggle. Age, market value, and basic performance are already
joined by `player_id`, which removes the live-scraping and name-matching risks
of a from-scratch FBref pipeline. Downloaded via [`kagglehub`](https://pypi.org/project/kagglehub/).

**Optional enrichment (later):** advanced metrics (xG, progressive actions) from
FBref / Understat via [`soccerdata`](https://soccerdata.readthedocs.io).

All sources are free. Raw data is git-ignored; `src/data_collection.py` reproduces it.

**Dataset version used:** Kaggle `davidcariboo/player-scores`, version 671
(fetched 2026-07-08) — 48,381 players, 1,889,407 appearances, 656,302 valuations,
88,944 games, 66 competitions.

> **Kaggle auth:** you need a free Kaggle API token. Create one at
> <https://www.kaggle.com/settings> → *Create New Token*, then save `kaggle.json`
> to `%USERPROFILE%\.kaggle\kaggle.json` (or set `KAGGLE_USERNAME` / `KAGGLE_KEY`).

## Status

 M0 (environment) and M1 (data acquisition) complete — raw tables are staged
in `data/raw/`.

 Extensive raw-data EDA in `notebooks/01_exploration.ipynb` (75 cells): schema/missingness
per table, position-mapping coverage, the full scoping funnel (leagues → seasons →
`MIN_MINUTES` → age range), and the age × position sample-size table, plus deep dives
on market value by age for each position, by league, and by nationality. Final
analysis-ready sample: **6,480 player-seasons across 2,970 unique players**. Confirmed
a real thin-tail risk (goalkeepers under ~24, all positions past ~35, and even more
pronounced once sliced by league/nationality) that curve fitting must account for.

 M2 (cleaning & feature engineering) implemented in `src/analysis.py`
(`build_player_seasons`, `attach_market_value`): reproduces the same 6,480
player-season / 2,970 player sample as the EDA scoping funnel, computes per-90
metrics, and attaches each season's nearest market valuation. Output is saved to
`data/processed/player_seasons.csv`.

 `notebooks/02_features_and_clustering.ipynb` (new): runs the M2 pipeline,
analyzes which age shows the largest drop in market value (peak ≈ age 21, largest
post-peak drop ≈ age 31, both overall and per position), and clusters player-seasons
into 4 interpretable profiles (e.g. young developing players, prime attackers, prime
defensive/GK regulars, aging declining-value veterans) — exploratory work ahead of
the formal M4/M5 milestones.

Next up: M3 (re-run EDA sanity checks against the processed table) and M4
(aging-curve modeling — quadratic + LOESS peak detection). See `PLAN.md` for the
full milestone breakdown and `protocol.md` for how notebooks are verified before
each commit.
