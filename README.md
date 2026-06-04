# Stellar Classification - Kaggle Playground Series S6E6
### Predicting Stellar Objects: GALAXY, STAR, or QSO

##  Project Overview

This project builds a machine learning model to classify astronomical objects as **GALAXY**, **STAR**, or **QSO** (Quasi-Stellar Object / Quasar) using photometric and spectroscopic features from the Sloan Digital Sky Survey (SDSS).

- **Competition:** Kaggle Playground Series - Season 6, Episode 6
- **Goal:** Predict the `class` label for each object in the test set
- **Evaluation Metric:** Balanced Accuracy
- **Classes:** GALAXY, STAR, QSO

##  File Structure

├── train.csv                # Training data (577,347 records with class labels)
├── test.csv                 # Test data (247,435 records, no labels)
├── sample_submission.csv    # Sample submission format provided by Kaggle
├── submission.csv           # Final predictions to submit to Kaggle
├── README.md                # Project documentation
└── project_doc.md           # Detailed project report

##  Dataset Description

| Feature             | Description                                        |
|---------------------|----------------------------------------------------|
| `id`                | Unique identifier                                  |
| `alpha`             | Right Ascension angle (sky coordinate)             |
| `delta`             | Declination angle (sky coordinate)                 |
| `u`                 | Ultraviolet filter magnitude                       |
| `g`                 | Green filter magnitude                             |
| `r`                 | Red filter magnitude                               |
| `i`                 | Near-infrared filter magnitude                     |
| `z`                 | Infrared filter magnitude                          |
| `redshift`          | Redshift value (light wavelength shift)            |
| `spectral_type`     | Stellar spectral classification (M, A/F, G/K, O/B)|
| `galaxy_population` | Galaxy population type (Red_Sequence, Blue_Cloud)  |
| `class` *(target)*  | Object class: GALAXY, STAR, QSO                    |

### Class Distribution (Training Set)
| Class  | Count   | Percentage |
|--------|---------|------------|
| GALAXY | 377,480 | 65.4%      |
| QSO    | 117,143 | 20.3%      |
| STAR   | 82,724  | 14.3%      |

##  Methodology

### 1. Feature Engineering
| Feature | Formula | Description                    |
|---------|---------|--------------------------------|
| `u_g`   | u − g   | UV to Green color index        |
| `g_r`   | g − r   | Green to Red color index       |
| `r_i`   | r − i   | Red to Near-IR color index     |
| `i_z`   | i − z   | Near-IR to IR color index      |
| `g_z`   | g − z   | Green to IR color index        |

### 2. Encoding
- `spectral_type` → Label Encoded (M, A/F, G/K, O/B → integers)
- `galaxy_population` → Binary (Red_Sequence = 1, Blue_Cloud = 0)

### 3. Model
- **Algorithm:** Random Forest Classifier
- **Parameters:** 100 trees, max depth 15, n_jobs = -1

##  Results

| Metric                        | Value      |
|-------------------------------|------------|
| CV Balanced Accuracy (sample) | **93.35%** |
| CV Std Dev                    | ± 0.28%    |
| Test Predictions Generated    | 247,435    |

### Predicted Class Distribution
| Class  | Count   |
|--------|---------|
| GALAXY | 162,692 |
| QSO    | 50,422  |
| STAR   | 34,321  |

### Top Feature Importances
| Feature  | Importance |
|----------|------------|
| redshift | 27.01%     |
| g_z      | 20.15%     |
| g_r      | 10.21%     |
| z        | 6.07%      |
| g        | 5.21%      |

##  Requirements

pandas
numpy
scikit-learn
Install via:
```bash
pip install pandas numpy scikit-learn

## Key Insights

- **Redshift** is the most important feature — QSOs have very high redshifts
- **Color indices** (g−z, g−r) are powerful discriminators between object types
- **Galaxy population** type helps separate GALAXY from STAR/QSO
- The dataset is imbalanced — balanced accuracy metric accounts for this
