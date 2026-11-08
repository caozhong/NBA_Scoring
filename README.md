# NBA Player Scoring — Final Machine Learning Capstone

## Executive summary
**Question:** Which player statistics are associated with NBA points per game, and how well can statistical models explain differences between players?

This project studies **2024–25 NBA regular-season per-game player statistics** for players who appeared in at least 10 games. It explores scoring distributions and playing time, then compares a simple average, linear regression, Ridge regression, random forest, and gradient boosting. Five-fold cross-validation and hyperparameter search support a fair comparison. The main accuracy measure is **mean absolute error (MAE)**: the average difference between a model's estimate and actual points per game.

**Important distinction:** These models use statistics recorded in the **same season** as the scoring totals. Shooting attempts and shooting percentages are directly linked to how points are calculated. Thus the models explain existing season statistics; they **cannot be treated as reliable forecasts of future scoring**.

## Why this matters
Coaches, analysts, and basketball fans want a simple way to understand which measures accompany high scoring. This analysis provides an accessible overview and shows why apparently excellent model accuracy can be misleading without considering how variables are related.

## Data and scope
- Source: [NBA official traditional player statistics](https://www.nba.com/stats/players/traditional), 2024–25 regular season, per-game view. The **user-provided original CSV is included** for full reproducibility.
- The CSV contains **569 records and 69 columns**; filtering to players with at least 10 games yields **508 players**.
- Outcome: `PTS` (points per game); input variables include minutes, games, shooting volume and accuracy, assists, rebounds, steals, blocks, turnovers and fouls.
- The analysis uses a fixed 80/20 split (**406 training and 102 test players**) and five-fold cross-validation on training data.

## Verified results (executed on the included CSV)

| Model | Holdout MAE (PPG) | Holdout RMSE (PPG) | Holdout R² |
|---|---:|---:|---:|
| Mean-only baseline | 5.145 | 6.543 | -0.009 |
| Linear regression | 0.377 | 0.531 | 0.993 |
| Ridge regression | 0.379 | 0.533 | 0.993 |
| Random forest | 0.494 | 0.727 | 0.988 |
| Gradient boosting | **0.333** | **0.471** | **0.995** |

**Selection protocol:** Ridge was selected by **training-set cross-validation** (rather than by looking at test scores). Gradient boosting achieved the smallest *observed* test error; this does not retroactively change the CV-selected model. The combined notebook documents the grid searches and metrics.

**Most important associations:** In the cross-validation-selected Ridge model, field-goal attempts (`FGA`) dominated held-out permutation importance (MAE increase about **4.934 PPG**), followed by free-throw attempts (`FTA`, **1.055 PPG**) and field-goal percentage (`FG_PCT`, **0.422 PPG**). These are not causal effects and largely reflect how points are generated.

**Sensitivity test:** Excluding shooting-volume and shooting-accuracy variables raised context-only Ridge **training cross-validation MAE to 1.760 PPG**. This illustrates how much the strikingly low error depends on contemporaneous scoring components. Do not compare this training CV estimate as though it were a holdout score.

**Practical meaning:** Even a test MAE of 0.333 PPG does **not** mean we can predict next-season or next-game performance within one point. These are retrospective, same-season associations, not forward-looking forecasts.

## Actionable recommendations
1. Use the descriptive plots to discuss player scoring patterns and playing time.
2. Treat same-season shooting variables as **explanations of observed scoring**, not independent early-warning signals.
3. Before using models for scouting or forecasting, use only information known **before** the period being predicted and validate against a later season.
4. Review model errors separately for low-minute players, high-volume scorers and player roles.

## Next steps
Add multiple seasons, engineer previous-season performance indicators, test on a future season, examine uncertainty and stability, and investigate whether the relationships differ across playing-time groups.

## Project files
- [Combined Jupyter Notebook — NBA scoring analysis](NBA_Scoring_Final_CSV_Load%282%29.ipynb): data preparation, exploratory analysis, baseline regression, cross-validation, grid search, model comparison, holdout evaluation, feature importance, sensitivity analysis, and conclusions.
- `data/nba_player_stats.csv`: original source dataset (569 records). Place the supplied CSV in the `data/` directory under this filename so the notebook can load it.
- `README.md`: project summary, findings, recommendations, and reproducibility instructions.

## Software requirements
The notebook uses **Python 3** and the following packages:

- `numpy` — numerical calculations
- `pandas` — data cleaning and analysis
- `matplotlib` — charts and diagnostic plots
- `seaborn` — statistical visualizations
- `scikit-learn` — preprocessing, regression models, cross-validation, hyperparameter tuning, and evaluation
- `jupyter` — running the `.ipynb` notebook (JupyterLab or Jupyter Notebook)

Install the packages with:

```bash
python -m pip install numpy pandas matplotlib seaborn scikit-learn jupyter
```

`pathlib` is also used but is included in Python's standard library and does not require installation.

## How to reproduce
1. Place the supplied NBA statistics CSV at `data/nba_player_stats.csv` relative to the project root.
2. Install the packages listed in **Software requirements** above.
3. From the project root, launch Jupyter (`jupyter notebook` or `jupyter lab`).
4. Open [`NBA_Scoring_Final_CSV_Load.ipynb`](NBA_Scoring_Final_CSV_Load%282%29.ipynb) and select **Run All**.
5. Review the notebook's figures, cross-validation results, holdout evaluation, and conclusions.

## Limitations
One season only; observational data; minimum-game threshold; possible variation in exported data; same-season target reconstruction; feature correlations; and no external-season validation. Correlation and feature importance are **not** causal claims.

## Contact
Questions and feedback can be submitted through the GitHub repository.
