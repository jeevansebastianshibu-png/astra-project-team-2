# Hack the Detector — Phishing URL Detector

Real-time and batch AI detection of phishing websites from URL and page-level signals.

Two models, one dataset:

- **Full model** (`models/full_model.pkl`) — Random Forest on all 30 handcrafted
  features (SSL state, domain age, web traffic, HTML behaviour, ...). Most
  accurate; needs a few seconds of network lookups per URL. Batch/offline use.
- **Lexical model** (`models/lexical_model.pkl`) — Random Forest on the 8
  features computable from the URL text alone. Zero network calls, instant
  verdict. Powers the live demo.

Trained on the UCI "Phishing Websites" dataset (Mohammad, Thabtah & McCluskey),
11,055 labelled sites, bundled at `data/dataset.csv` so training is fully
reproducible offline.

## Quickstart

```bash
pip install -r requirements.txt

python src/train_full_model.py      # trains & compares 6 classifiers, saves plots to reports/
python src/train_lexical_model.py   # trains the live-demo model

python src/app.py                   # starts the demo at http://127.0.0.1:5000
```

Then open **http://127.0.0.1:5000**, paste a URL, and get an instant verdict
with a signal-by-signal breakdown of what fired and why.

## Project structure

```
data/dataset.csv              UCI Phishing Websites dataset (11,055 rows)
src/columns.py                feature name definitions
src/train_full_model.py       trains & compares all 6 models, saves reports/plots
src/lexical_features.py       offline URL feature extractor (8 features, no network)
src/train_lexical_model.py    trains the live-demo model
src/app.py + templates/       Flask demo application
models/                       saved trained models (full_model.pkl, lexical_model.pkl)
reports/                      EDA plots, confusion matrix, feature importance, metrics
```

## Results

| Model | Test Accuracy | Precision | Recall | F1-score |
|---|---|---|---|---|
| **Random Forest (full, 30 features)** | **97.4%** | 97.1% | 98.3% | 97.7% |
| Gradient Boosting | 95.3% | 95.3% | 96.3% | 95.8% |
| K-Nearest Neighbors | 95.0% | 94.7% | 96.3% | 95.5% |
| SVM (RBF) | 94.9% | 94.0% | 97.0% | 95.5% |
| Decision Tree | 94.8% | 95.7% | 94.9% | 95.3% |
| Logistic Regression | 92.9% | 92.4% | 95.0% | 93.7% |
| **Random Forest (lexical, 8 features)** | **73.3%** | 81.9% | 66.8% | 73.6% |

Full numbers regenerate in `reports/model_comparison.csv` and
`models/lexical_model_meta.json` every time you re-run training.

## Live demo, no local setup

`live_demo.html` in this folder is a self-contained, offline, single-file
version of the lexical detector (logistic-regression re-fit of the same 8
features, ~69.5% test accuracy) — open it directly in a browser with no
Python, no Flask, nothing to install. Handy for a presentation laptop with
no dev environment set up.
