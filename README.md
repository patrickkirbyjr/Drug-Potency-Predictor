# Project Objective and Scope

Modern medicine single-handedly proves what humans are capable of. Pay a visit to one outpatient center and you will find people undergoing minute-long procedures for ailments that would have resulted in painful death just a few decades ago. Despite these advances, people still die of cancer every day. I believe that one of the greatest tools at medicine’s disposal is technology. Specifically, I believe that research in data science could lead to the next major breakthrough in treating and curing cancer.

My project set out to analyze large research datasets in order to predict the effectiveness of cancer treatment medications. The goal was to predict the effectiveness in specific cell lines based on specific genetic mutations present in the patient’s deoxyribonucleic acid (DNA).

Effectiveness of drug treatment was measured using the half-maximal inhibitory concentration (**IC50**), a common metric used throughout the pharmaceutical industry. IC50 measures the potency of the drug and gives practitioners an idea of how well it may eliminate cancerous cells. Potent drugs have low IC50 values while less-powerful drugs have higher IC50 values. I hypothesized that analyzing a large enough dataset would lead to models being able to accurately predict IC50 when given a combination of drug and genetic mutation.

I set out to determine how such models would perform across a pan-cancer dataset with a high number of drug-mutation combinations. If successful, similar models could be trained with larger datasets and practitioners could use the prediction information when constructing cancer treatment plans. The eventual goal would be for practitioners to have a model powerful enough to create a hyper-specific treatment plan for their patients based on their specific genetic mutations.

This project exists as a proof-of-concept. This study was not meant to be implemented into the healthcare industry at this time, but serves as a test to determine the following:

- **Can models accurately predict IC50 values when given a large dataset featuring drug and genetic mutation data?**
- **Which models, if any, provide the most statistically-sound predictions for such data?**

---

## Data Acquisition

Data was acquired from **The Genomics of Drug Sensitivity in Cancer Project** ([cancerrxgene.org](https://www.cancerrxgene.org)). This project was co-sponsored by the Wellcome Sanger Institute in the United Kingdom and Massachusetts General Hospital Cancer Center in the United States.

The Genomics of Drug Sensitivity in Cancer Project was an excellent source for this project. Their website offered free and customizable downloads of the data used throughout their own published research. I was able to download separate files containing drug and genetic feature data for use in my project. The data was ready for model use with only a moderate amount of preparation. Additionally, the datasets were large enough to meet the goals of the project.

---

## Exploratory Data Analysis

Basic statistical analysis was conducted on the datasets throughout the entire process. My main goals were to check for missing values, observe extreme outliers, and quantify standard deviation for later evaluation.

I constructed a histogram to view the distribution of IC50 values across the entire dataset. The values from the original dataset had to be transformed logarithmically in order to get them back to proper concentrations (micromolar). The histogram displayed a severe skew of the data. This could have meant one of two possible situations:

1. The data was heavily skewed due to the presence of outliers.
2. The data was heavily skewed due to extreme differences in drug potencies.

After basic statistical analysis and a look at distribution of IC50 values, it was decided that all missing values would be dropped. All other data would remain in the dataset for future model training. Future models should evaluate each drug to determine if the outliers were potential potencies for such drugs or if they were not administered in that concentration.

---

## Data Preparation and Cleaning

A series of steps were taken to prepare the data for use in machine learning models:

- The genetic dataset was transformed into a pivot table, with each row representing a different cell line and each column representing a different mutation. Values were encoded as:
  - **0.0** – No genetic mutation
  - **1.0** – Specific mutation present
- Genetic and drug datasets were merged using **COSMIC ID**, a unique identifier for each cell line.
- Missing values were dropped to train models using only real research data. The impact of deletion was minimal due to the small number of NAs.
- Outliers within IC50 values were preserved due to lack of pharmacological expertise; future studies should review outliers with domain experts.
- Unnecessary columns were dropped, retaining only:
  - **Drug ID** (categorical feature)
  - **All studied mutations** (categorical features)
  - **IC50** (continuous target)

**Encoding and Scaling:**

- **Drug ID** was encoded using **OneHotEncoder**.
- Mutation columns were already in a pivoted form, so further encoding was unnecessary.
- The dataset was split into **training** and **testing** sets, and features were scaled using **StandardScaler** (required for Ridge and Lasso regressions).

**Dimensionality Reduction:**

- Due to the high number of features, **Principal Component Analysis (PCA)** was applied.
- Features were reduced from **607 to 172**, maintaining approximately **50% explained variance** due to computational limitations.

---

## Model Training

Multiple machine learning models were used to predict IC50 values based on drug and genetic mutation combinations. All models used the same features and target variables.

### 1. Random Forest Regression

- Tuned using **RandomizedSearchCV** with parameters:
  - Number of Estimators: 100–200
  - Max Depth: None, 15, 30
  - Min Samples Split: 2, 5
  - Min Samples Leaf: 1, 2
  - Max Features: Sqrt
- **30 iterations**, 3-fold CV, scored on **R²**.

### 2. Ridge Regression

- Used to identify strong correlations between features and IC50.
- Performed well and required relatively low processing power.

### 3. Lasso Regression

- Useful for feature selection, shrinking unimportant features.
- Well-suited due to the large number of features and limited computational resources.

### 4. Neural Network (Regression)

- Anticipated to perform best due to the high dimensionality of the dataset.
- Tuned using **RandomizedSearchCV** with early stopping:
  - Activation: Relu, Tanh
  - Hidden Layers: (50,), (100,), (50,50), (100,50)
  - Alpha: 0.0001, 0.001, 0.01, 0.1
  - Learning Rates: 0.001, 0.01, 0.1
- **20 iterations**, 3-fold CV, scored on **R²**.

### 5. CatBoost Regression

- Selected for its ability to handle categorical features without encoding.
- Chosen for deployment as a proof-of-concept due to performance and simplicity.

---

## Model Evaluation

Models were evaluated using:

- **R-squared (R²)**
- **Mean Squared Error (MSE)**
- **Root Mean Squared Error (RMSE)**
- **Predicted vs. Actual plots**
- **Residual plots**

### Performance Metrics

| Model                    | R²        | MSE      | RMSE    |
|--------------------------|-----------|----------|---------|
| Random Forest            | 0.7224    | 2.1068   | 1.4515  |
| Tuned Random Forest      | 0.7541    | 1.8667   | 1.3663  |
| Ridge Regression         | 0.7600    | 1.8217   | 1.3497  |
| Lasso Regression         | 0.7598    | 1.8235   | 1.3504  |
| Neural Network           | 0.8091    | 1.4489   | 1.2037  |
| **Tuned Neural Network** | **0.8209**| **1.3595**| **1.1660** |
| CatBoost                 | 0.6440    | 2.7543   | 1.6596  |

---

## Model Comparison

- The **tuned neural network** was the top-performing model with the highest **R²** and lowest error values.
- Its predicted vs. actual plot showed tight grouping with minimal bias.
- Residuals were evenly spread, indicating no major systemic errors.
- The learning curve indicated steady performance improvement with no overfitting.

**Conclusion:**  
The tuned neural network shows strong potential as a predictive model for IC50 values, serving as a proof-of-concept for future cancer treatment personalization.
