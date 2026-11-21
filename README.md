# BioAI-X

## Day 1: Dataset Preparation (`GSE76275`)
- **Dataset:** `GSE76275` (*Homo sapiens* breast cancer microarray data, platform GPL570)
- **Samples:** 265 total (198 TNBC = `1`, 67 non-TNBC = `0`)
- **Notebook:** `day1_tnbc_data.ipynb`

## Day 2: Probe Mapping, DEG Analysis & Visualizations (`GSE76275`)
- **Notebook:** `day2_deg_analysis.ipynb`
- **Probe-to-Gene Mapping:** Mapped GPL570 probe IDs to **21,655** gene symbols across **265** samples (max expression ~15.62, no log2 conversion needed).
- **Differential Expression (DEGs):** Ran Welch's t-test (`TNBC` vs. `non-TNBC`) with Benjamini-Hochberg FDR correction, identifying **13,496** significant genes (`FDR < 0.05`).
- **Outputs:** `top500_DEGs.csv`, `tnbc_top500.csv`, `pca.png`, and `volcano.png`.

Day 2: DEG analysis and plots done.

## Day 3: Leak-Free Machine Learning Classification & LASSO Biomarker Selection (`day3_ml_model.ipynb`)

### Objective
Train and evaluate supervised machine learning classifiers on the `GSE76275` gene expression matrix (`265` samples × `21,655` genes) to distinguish **Triple-Negative Breast Cancer (TNBC, `n = 198`)** from **Non-TNBC (`n = 67`)** without data leakage, and extract a minimal, high-signal biomarker signature.

### Leak-Free Methodology
* **Train/Test Split:** Stratified 70/30 split (`random_state=42`, `stratify=y`) separating `185` training samples (`139` TNBC, `46` Non-TNBC) from `80` held-out test samples (`59` TNBC, `21` Non-TNBC).
* **Pipeline Architecture (Zero Data Leakage):** Rather than pre-filtering features on the full dataset, univariate feature selection (`SelectKBest(f_classif, k=500)`) and feature scaling (`StandardScaler`) were encapsulated strictly inside scikit-learn `Pipeline` objects so transformations are fitted only inside training folds:
  1. **L1-Regularized Logistic Regression (`lasso`):** `StandardScaler` (`sc`) → `SelectKBest(k=500)` (`sel`) → `LogisticRegression(penalty="l1")` (`clf`)
  2. **Random Forest Classifier (`rf`):** `SelectKBest(k=500)` (`sel`) → `RandomForestClassifier` (`clf`)
* **Validation & Evaluation:** Evaluated via 5-fold `StratifiedKFold` cross-validation (`roc_auc`) on `X_train`, followed by final evaluation on the untouched `X_test` set.

### Model Performance
* **LASSO Logistic Regression:** 5-Fold CV ROC-AUC = `[YOUR_LASSO_CV_AUC]` | Held-Out Test ROC-AUC = `[YOUR_LASSO_TEST_AUC]`
* **Random Forest Classifier:** 5-Fold CV ROC-AUC = `[YOUR_RF_CV_AUC]` | Held-Out Test ROC-AUC = `[YOUR_RF_TEST_AUC]`

![ROC Curve](roc_curve.png)

### Sparse Biomarker Signature (`top20_biomarkers.csv`)
L1 regularization compressed the 500 `SelectKBest` candidate genes down to a **6-gene non-zero predictive signature**:

| Rank | Gene Symbol | LASSO Coefficient | Absolute Weight | Regulation in TNBC |
| :---: | :--- | :---: | :---: | :--- |
| 1 | `COL18A1-AS1` | -1.3859 | 1.3859 | Down-regulated (-) |
| 2 | `AX746627` | -0.7040 | 0.7040 | Down-regulated (-) |
| 3 | `FZD9` | +0.2604 | 0.2604 | Up-regulated (+) |
| 4 | `C5orf49` | -0.1098 | 0.1098 | Down-regulated (-) |
| 5 | `IL5RA` | +0.0889 | 0.0889 | Up-regulated (+) |
| 6 | `RP11-55K13.1` | -0.0494 | 0.0494 | Down-regulated (-) |

### Day 3 Files Added
* `day3_ml_model.ipynb` — End-to-end Colab notebook implementing stratified splitting, leak-free `Pipeline` training, 5-fold CV, test set evaluation, and LASSO coefficient extraction.
* `roc_curve.png` — Held-out test set ROC curve comparing classifier discrimination performance.
* `top20_biomarkers.csv` — Ranked non-zero LASSO biomarker genes with signed coefficients and expression direction.
