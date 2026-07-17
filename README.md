# Speed Dating — Clustering

Clustering analysis of the Speed Dating Experiment dataset, exploring what natural
participant types emerge and how they relate to matching success. Course project for
Data Mining 2 (Istraživanje podataka 2), Faculty of Mathematics, University of Belgrade.

## Overview

The dataset records 8378 four-minute speed-dating encounters (551 unique participants
across 21 events). Each row is one date from one participant's perspective. The goal is
unsupervised: to discover natural groupings of participants from their interests,
preferences, and self-ratings — without using the match outcome to build the clusters.

Participants are clustered under several different feature sets, and the resulting groups
are compared and interpreted. Five clustering algorithms are compared in detail.

## Data

- Source: [OpenML dataset 40536](https://www.openml.org/d/40536) (Speed Dating)
- 8378 rows, 121 attributes
- Target: `match` (binary), overall rate 0.165
- Note: the OpenML version omits the original `iid` participant identifier, which was
  reconstructed from invariant attributes during preprocessing.

## Repository structure

```
speed-dating-clustering/
├── data/
│   ├── raw/                 # original dataset (speed_dating_raw.csv)
│   └── processed/           # preprocessed feature sets, metadata, cluster labels
├── models/                  # saved scaler, imputers, and clustering models
├── notebooks/
│   ├── 01_preprocessing.ipynb
│   └── 02_clustering.ipynb
├── requirements.txt
└── README.md
```

## Processed data files

- `X_scaled.csv` — full scaled feature set (with outcome proxies)
- `X_scaled_no_outcome.csv` — scaled features excluding outcome proxies (used for clustering)
- `X_unscaled.csv` — features in original units (for interpretation)
- `metadata.csv` — wave, match, reconstructed person_id, row_id
- `cluster_labels.csv` — cluster assignments for each feature set

## How to run

```bash
python -m venv venv
source venv/bin/activate        # on Windows: venv\Scripts\activate
pip install -r requirements.txt
jupyter notebook
```

Run the notebooks in order:

1. `notebooks/01_preprocessing.ipynb` — cleaning, encoding, block normalization, imputation
2. `notebooks/02_clustering.ipynb` — clustering, algorithm comparison, interpretation

Both notebooks are reproducible end to end (Kernel → Restart & Run All). A fixed random
seed (42) is used throughout.

## Method notes

- **Outcome held out:** `like` and `guess_prob_liked` are excluded from clustering so that
  match rate remains an independent validation signal.
- **Block normalization:** features are grouped into conceptual blocks, each scaled to equal
  total variance, so that concepts with many columns do not dominate those with few.
- **Two-stage imputation:** copy-within-person, then kNN. A separate outcome-free imputation
  prevents leakage through kNN neighbours.
- **Five algorithms compared:** k-means (reference), agglomerative, Gaussian mixture, BIRCH,
  and DBSCAN — each with its key parameter selected explicitly.


## Key findings

- **Person profile** splits primarily by gender, then (clustering within each sex) into
  three types: one male group and two female groups. These differ significantly in match
  rate (p ≈ 0.003).
- **Interests** split by lifestyle — sporty, cultural, and (at k=3) socially active —
  crossing gender. These also differ in match rate at k=2 (p ≈ 0.015).
- **Preferences** split by what people value (looks / character / ambition) but do **not**
  differ in match rate (p ≈ 0.17).
- Interests and preferences are themselves related (p < 0.001).
- On the full feature set, a single binary attribute — `met` (whether the pair had met
  before) — forms its own cluster with double the match rate.
- Main takeaway: in this sample, *who a person is* relates to matching success, while
  *what they say they want* does not.
