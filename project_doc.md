# Project Documentation
## Stellar Classification — Kaggle Playground Series S6E6

---

## 1. Problem Statement

The goal of this competition is to classify astronomical objects observed by the **Sloan Digital Sky Survey (SDSS)** into one of three categories:

| Class  | Description |
|--------|-------------|
| **GALAXY** | A system of millions or billions of stars |
| **STAR**   | A luminous spheroid of plasma held by gravity |
| **QSO**    | Quasi-Stellar Object (Quasar) — an extremely luminous active galactic nucleus |

The competition uses **balanced accuracy** as its evaluation metric, which accounts for class imbalance by computing accuracy per class and averaging them.

---

## 2. Dataset Summary

### 2.1 Files
| File                  | Rows    | Columns | Description                    |
|-----------------------|---------|---------|--------------------------------|
| train.csv             | 577,347 | 12      | Labeled training data          |
| test.csv              | 247,435 | 11      | Unlabeled test data            |
| sample_submission.csv | 247,435 | 2       | Required submission format     |
| submission.csv        | 247,435 | 2       | Our final predictions          |

### 2.2 Features

**Photometric Features (Filter Magnitudes):**
- `u` — Ultraviolet band
- `g` — Green band
- `r` — Red band
- `i` — Near-infrared band
- `z` — Infrared band

**Astrometric Features:**
- `alpha` — Right Ascension (RA), celestial longitude
- `delta` — Declination (Dec), celestial latitude

**Spectroscopic Features:**
- `redshift` — Measure of how much the light from an object has been stretched; key indicator of distance and object type
- `spectral_type` — Stellar classification (O/B = hot blue stars, A/F = white/yellow, G/K = yellow/orange, M = red/cool)
- `galaxy_population` — Galaxy environment (Red_Sequence = older passive, Blue_Cloud = younger star-forming)

### 2.3 Class Distribution
| Class  | Train Count | Train %  | Predicted Test | Predicted Test % |
|--------|-------------|----------|----------------|------------------|
| GALAXY | 377,480     | 65.4%    | 162,692        | 65.7%            |
| QSO    | 117,143     | 20.3%    | 50,422         | 20.4%            |
| STAR   | 82,724      | 14.3%    | 34,321         | 13.9%            |

---

## 3. Exploratory Data Analysis

### 3.1 Key Observations

- **No missing values** in any column across train and test sets
- **Redshift** shows distinct distributions per class:
  - STARs cluster near redshift ≈ 0
  - GALAXies have moderate redshift (0.0–1.0)
  - QSOs show high redshift values (1.0–7.0+)
- **Color indices** (differences between filter magnitudes) are strong separators:
  - QSOs are typically bluer (lower color index)
  - GALAXies span a wider range
  - STARs follow the stellar main sequence
- `spectral_type` and `galaxy_population` provide additional classification context

### 3.2 Feature Correlations
The photometric bands (u, g, r, i, z) are highly correlated with each other. Their **differences (color indices)** capture more discriminative information than the raw magnitudes.

---

## 4. Feature Engineering

The following derived features were created:

| Feature | Formula | Astronomical Meaning               |
|---------|---------|-------------------------------------|
| `u_g`   | u − g   | UV excess, identifies hot objects   |
| `g_r`   | g − r   | Standard optical color              |
| `r_i`   | r − i   | Red/IR transition color             |
| `i_z`   | i − z   | Near-IR color                       |
| `g_z`   | g − z   | Broad optical–IR color baseline     |

**Encoding:**
- `spectral_type`: Label Encoded → {A/F: 0, G/K: 1, M: 2, O/B: 3}
- `galaxy_population`: Binary → Red_Sequence = 1, Blue_Cloud = 0

---

## 5. Modeling

### 5.1 Algorithm: Random Forest Classifier

Random Forest was chosen because:
- Handles mixed feature types (numerical + encoded categorical) well
- Robust to outliers in photometric measurements
- Provides feature importance rankings
- Parallelizable (n_jobs = -1) for large datasets

### 5.2 Hyperparameters
| Parameter          | Value |
|--------------------|-------|
| n_estimators       | 100   |
| max_depth          | 15    |
| min_samples_split  | 2     |
| random_state       | 42    |
| n_jobs             | -1    |

### 5.3 Training Strategy
- Due to dataset size (577K rows), trained on a **stratified random sample of 150,000 rows**
- Stratified sampling ensures all 3 classes are proportionally represented
- Cross-validation (3-fold) performed on a 50,000-row sample for speed

---

## 6. Results

### 6.1 Model Performance
| Metric                        | Score      |
|-------------------------------|------------|
| CV Balanced Accuracy (sample) | **93.35%** |
| CV Standard Deviation         | ± 0.28%    |

### 6.2 Feature Importances
| Rank | Feature      | Importance |
|------|--------------|------------|
| 1    | redshift     | 27.01%     |
| 2    | g_z          | 20.15%     |
| 3    | g_r          | 10.21%     |
| 4    | z            | 6.07%      |
| 5    | g            | 5.21%      |
| 6    | r_i          | 4.98%      |
| 7    | galaxy_pop   | 4.58%      |
| 8    | u_g          | 4.15%      |
| 9    | r            | 3.96%      |
| 10   | i            | 3.87%      |

**Key Finding:** `redshift` alone contributes over 27% of predictive power, consistent with astronomical theory — QSOs are identified by extremely high redshifts.

---

## 7. Submission

### 7.1 Format
```
id,class
577347,STAR
577348,GALAXY
577349,STAR
...
```

### 7.2 Submission Statistics
- Total predictions: **247,435**
- GALAXY: 162,692 (65.7%)
- QSO: 50,422 (20.4%)
- STAR: 34,321 (13.9%)

---

## 8. Potential Improvements

| Idea | Expected Impact |
|------|----------------|
| Train on full 577K dataset (not sample) | +1–2% accuracy |
| XGBoost or LightGBM | Better performance on tabular data |
| Additional color indices (u−r, u−z) | More spectral coverage |
| Hyperparameter tuning (GridSearch/Optuna) | Optimized model |
| Stacking/ensembling multiple models | More robust predictions |
| UMAP/t-SNE for cluster analysis | Better feature understanding |

---

## 9. Conclusion

This project successfully built a **Random Forest classifier** achieving **93.35% balanced accuracy** on the stellar classification task. The most discriminative feature is `redshift`, which aligns with established astronomical knowledge. Color indices derived from photometric filter magnitudes provide additional strong signals for differentiating GALAXY, STAR, and QSO objects.

---

## 10. References

- [Stellar Classification Dataset (SDSS17)](https://www.kaggle.com/datasets/fedesoriano/stellar-classification-dataset-sdss17)
- [Kaggle Playground Series S6E6](https://www.kaggle.com/competitions/playground-series-s6e6)
- [Balanced Accuracy Score — scikit-learn](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.balanced_accuracy_score.html)
- [Sloan Digital Sky Survey](https://www.sdss.org/)
