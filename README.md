# Lung Cancer Risk Estimator

A small web app that estimates a patient's lung-cancer risk from a short symptom
profile, and shows **which factors drove each estimate**. It is backed by a
logistic-regression model trained on a real clinical survey of 309 patients
(held-out ROC-AUC ≈ 0.96).

> **Not a diagnostic tool.** This is a statistical screening aid built from a
> self-reported survey dataset. A high estimate means clinical follow-up may be
> worth considering — nothing more.

---

## Quick start

### Windows (easiest)
Double-click **`run.bat`**. It installs what it needs the first time, starts the
app, and opens your browser automatically.

### Any operating system (manual)
```bash
pip install -r requirements.txt
python app.py
```
Then open <http://127.0.0.1:5000> (it opens on its own too).

Requires **Python 3.9+**. To stop the app, close its window or press `Ctrl+C`.

---

## Using it

1. Set the patient's **age** and **gender**.
2. Tap the **symptoms & history** that apply (smoking, coughing, wheezing, …).
3. Press **Estimate risk**.

You get back:
- a **risk probability** and band (Low / Moderate / Elevated / High), and
- the **top factors behind that specific estimate** — each shown as raising (▲) or
  lowering (▼) the risk, sized by how much it mattered.

The **Example** button fills a realistic high-risk case so you can see it work.

---

## How it works

- **Data:** the public *Lung Cancer Survey* dataset — 309 patients, 15 features
  (age, gender, and 13 yes/no symptom & lifestyle flags), target `LUNG_CANCER`.
- **Model:** `StandardScaler` + `LogisticRegression` (balanced class weights).
  Logistic regression is used deliberately: its coefficients are directly
  interpretable, so the app can explain *why* it predicted what it did rather than
  acting as a black box.
- **Honest score:** the app trains on a 75% split to measure a held-out ROC-AUC
  (printed on startup), then refits on all 309 records for serving.
- **Explanation:** for each prediction, per-feature contribution =
  `coefficient × standardized input`; the largest are surfaced as the drivers.

Input is validated server-side (age 0–120, flags 0/1) before it reaches the model.

---

## What's in the folder

| File | Purpose |
|------|---------|
| `app.py` | The web app — model training, prediction API, and UI in one file |
| `run.bat` | Windows one-click launcher |
| `requirements.txt` | Python dependencies |
| `survey_lung_cancer.csv` | Real survey data the app is trained on |
| `ml_pipeline.py` | Benchmark of 6 classifiers (LogReg, SVM, k-NN, MLP, RandomForest, HistGB) with ROC / PR / permutation-importance figures |
| `dashboard.html` | Standalone interactive dashboard of the benchmark results (open in any browser) |
| `analysis.py` | The original report's statistical pipeline (correlation + linear regression) |
| `generate_data.py` | Generates the 5,000-row synthetic cohort used as a benchmark comparison |
| `lung_cancer.csv` | The generated synthetic dataset |

### Running the analysis (optional)
```bash
python ml_pipeline.py          # benchmarks all 6 models on both datasets
python ml_pipeline.py your.csv # or point it at any CSV (auto-detects the target)
```
This writes `*_models.png` and `*_results.json` for each dataset.

---

## Data source

Lung Cancer Survey dataset (309 records), mirrored at
<https://github.com/ShinjiniShome/lung_cancer_survey_dataviz>.
Self-reported survey data — suitable for a demonstration project, not for clinical use.
