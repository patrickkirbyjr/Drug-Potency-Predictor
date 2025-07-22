# Predicting Drug Potency in Cancer Cell Lines

This project demonstrates a **proof‑of‑concept machine learning pipeline** to predict the **half‑maximal inhibitory concentration (IC50)** of cancer treatment drugs based on **cell line genetic mutations**. By leveraging genomic data and modern regression techniques, the aim is to advance personalized medicine and improve treatment planning for cancer patients.

---

## Objective

- **Goal:** Predict drug effectiveness (IC50) for specific genetic mutation profiles.
- **Why:** IC50 indicates drug potency — lower values mean stronger cancer‑killing effects.
- **Vision:** Enable hyper‑specific, mutation‑driven cancer treatment plans in clinical settings.

---

## Dataset

- **Source:** [Genomics of Drug Sensitivity in Cancer (GDSC)](https://www.cancerrxgene.org)
- **Data Used:**
  - Drug identifiers (`Drug ID`)
  - Binary genetic mutation features (0 = no mutation, 1 = mutation present)
  - IC50 values (target variable)

**Data Processing Steps:**
- Merged drug and mutation datasets on **COSMIC ID**.
- Dropped missing values; preserved outliers for biological interpretation.
- Encoded `Drug ID` via **OneHotEncoder**; mutations were already pivoted.
- Standardized features and applied **PCA** (reduced from 607 → 172 features, ~50% variance).

---

## Models & Training

**Regression models used:**
- **Random Forest**
- **Ridge Regression**
- **Lasso Regression**
- **Neural Network** (MLPRegressor)
- **CatBoost Regression**

**Key techniques:**
- Hyperparameter tuning with **RandomizedSearchCV**.
- Train/test split with standardized scaling.
- Evaluated using **R²**, **MSE**, **RMSE**, and diagnostic plots.

---

## Results

| Model                    | R²        | MSE      | RMSE    |
|--------------------------|-----------|----------|---------|
| Random Forest            | 0.7224    | 2.1068   | 1.4515  |
| Tuned Random Forest      | 0.7541    | 1.8667   | 1.3663  |
| Ridge Regression         | 0.7600    | 1.8217   | 1.3497  |
| Lasso Regression         | 0.7598    | 1.8235   | 1.3504  |
| Neural Network           | 0.8091    | 1.4489   | 1.2037  |
| **Tuned Neural Network** | **0.8209**| **1.3595**| **1.1660** |
| CatBoost                 | 0.6440    | 2.7543   | 1.6596  |

**Best Model:**  
- **Tuned Neural Network** – highest R² (0.821) and lowest RMSE (1.166).  
- Plots showed tight predicted vs. actual grouping and balanced residuals.
- Demonstrated promise for mutation‑driven drug response prediction.

---

## Key Takeaways

- Genomic features can effectively predict drug potency (IC50) in cell lines.
- Neural networks outperform traditional regressions in handling high‑dimensional mutation data.
- Further development with larger datasets and clinical validation could support **personalized cancer treatment planning**.

---

## Next Steps

- Collaborate with pharmacologists to refine outlier handling.
- Train on larger, more diverse datasets for improved generalization.
- Explore cloud‑based deployment for real‑time clinical integration.

---

## Project Status

This project is a **proof‑of‑concept** and is **not intended for clinical use** at this stage.  
It demonstrates the **feasibility of mutation‑driven IC50 prediction** using machine learning.
