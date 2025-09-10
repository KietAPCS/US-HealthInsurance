# US-HealthInsurance

## Health Insurance Charges — Regression & ANOVA Study

A reproducible statistical analysis of the US Health Insurance dataset using Python. The notebook explores how demographic and lifestyle factors (smoking, BMI, sex, children, region) impact medical charges via descriptive analysis, assumption checks, one-way and two-way ANOVA (with robust HC3 covariance), and a 2^k factorial design with estimated marginal means and adjusted post‑hoc contrasts.

### Repository layout

- `InsuranceAnova.ipynb` — main analysis notebook (EDA, ANOVA, robust methods, post‑hoc, plots)
- `data/insurance.csv` — dataset (1,338 rows) from Kaggle (see citation)

Note: In a few cells the notebook references `data2/insurance.csv`. If you only have `data/insurance.csv` (default here), update the path in those cells to `data/insurance.csv` before running.

### Dataset

- Source: Kaggle — “Medical Cost Personal Datasets”
  https://www.kaggle.com/datasets/mirichoi0218/insurance
- Variables (raw):
  - `age` (numeric), `sex` (male/female), `bmi` (numeric), `children` (integer),
    `smoker` (yes/no), `region` (northeast/northwest/southeast/southwest),
    `charges` (continuous target)
- Transformations used in the notebook:
  - `bmi_cat`: derived categorical BMI (not_obese vs obese, cutoff 30)
  - `children_bin`: derived from `children` (No if 0, Yes if >0)
  - `log_charges`: log transformation of `charges` to mitigate skew

### Research questions and hypotheses

1) Does smoking status significantly affect insurance charges?
   - H0: mean charges are equal for smokers vs non‑smokers; H1: different.

2) Do region and BMI category, individually or interactively, influence charges?
   - H0: no main effects (region, BMI) and no interaction; H1: at least one is significant.

3) What are the main and interaction effects in a 2^k factorial design?
   - Factors considered: smoker (Yes/No), children (No/Yes), sex (Male/Female). The analysis is extended to include BMI category resulting in a 2^4 model in later sections.

### Methods (high level)

- Data quality: check missing values and duplicates; cast categorical types.
- Feature engineering: `bmi_cat`, `children_bin`, `log_charges`.
- Descriptives & EDA: summaries, histograms, boxplots, interaction plots.
- Assumption checks: residual diagnostics, Shapiro–Wilk (normality), Levene (variance homogeneity), Durbin–Watson (independence).
- One‑way ANOVA: `charges ~ smoker` and `log_charges ~ smoker`; Welch t‑test, Mann–Whitney U, HC3‑robust SE for OLS, effect size (Hedges’ g), bootstrap CI.
- Two‑way ANOVA: `log_charges ~ C(bmi_cat) * C(region)` with classical and HC3‑robust Type II ANOVA; estimated marginal means (EMMs); Holm‑adjusted post‑hoc contrasts; interaction plots with CIs.
- Factorial design: `log_charges ~ C(smoker)*C(sex)*C(children_bin)*C(bmi_cat)` with HC3 robust ANOVA; adaptive EMMs and Holm‑adjusted contrasts; visualization utilities.

### Key findings (as reported in the notebook)

- Smoking status has a very large, statistically robust effect on charges.
  - Welch’s t‑test extremely significant; Mann–Whitney confirms distributional differences.
  - Effect size Hedges’ g is enormous; smokers’ median charges are several times those of non‑smokers.
  - Robust OLS with HC3 indicates smokers incur ~tens of thousands of USD more on average.

- BMI category is a strong predictor of charges; obese individuals pay ~25–30% more on average (on original scale) relative to non‑obese after log‑scale analysis.

- Region shows no statistically significant main effect after correction, and there is no strong evidence of BMI × region interaction; BMI effect is consistent across regions.

### How to run

1) Create and activate a virtual environment (Windows bash):

```bash
python -m venv .venv
source .venv/Scripts/activate
```

2) Install dependencies:

```bash
pip install -r requirements.txt
```

3) Ensure the dataset exists at `data/insurance.csv`.
   - If your notebook cells reference `data2/insurance.csv`, change them to `data/insurance.csv` or copy the file accordingly.

4) Open `InsuranceAnova.ipynb` and run cells top‑to‑bottom.

### Reproducibility notes

- Some analyses use randomization (e.g., bootstrap) with fixed seeds for stability.
- HC3 robust standard errors are used where variance heterogeneity is suspected.
- Post‑hoc uses Holm adjustment to control family‑wise error without assuming equal variances.

### Dependencies

Core scientific stack plus robust/ANOVA utilities and plotting:

- numpy, pandas, scipy, statsmodels, pingouin, patsy
- matplotlib, seaborn, plotly
- scikit‑learn (utilities), imbalanced‑learn (imported; not essential to ANOVA parts)
- jupyter (to run the notebook)

See `requirements.txt` for a minimal list.

### Troubleshooting

- ModuleNotFoundError (e.g., `pingouin`, `patsy`): install via `pip install -r requirements.txt`.
- File not found: update paths from `data2/insurance.csv` to `data/insurance.csv` or place the CSV accordingly.
- Plots not showing in Jupyter: ensure `%matplotlib inline` (or use the default) and that plotly offline is initialized (the notebook calls `pyo.init_notebook_mode()`).

### Citation

Mirza Choi (mirichoi0218). “Medical Cost Personal Datasets.” Kaggle. https://www.kaggle.com/datasets/mirichoi0218/insurance

Please respect the dataset’s license/terms of use as provided on Kaggle.

---

If you’d like, we can parameterize the data path and factor cutoffs (e.g., BMI threshold) and extract the robust EMM/post‑hoc helpers into a small utility module for reuse across notebooks.
