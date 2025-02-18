# 🏥 Risk Factors for Hepatocellular Carcinoma (HCC) in Patients with Type 2 Diabetes

### 📊 Predicting HCC risk based on comorbidities and alcohol consumption

## 📌 Context

Alcohol is a major contributor to over 700 diseases, including hepatocellular carcinoma (HCC), a severe form of liver cancer.
Patients with Type 2 Diabetes (T2D) are at an increased risk of HCC due to associated factors like obesity and hypertension.

## 🔍 Research Question:
Can we predict the risk of HCC in T2D patients based on clinical characteristics and recorded alcohol consumption?

## 🏥 Dataset

Source: Hospitalization records of T2D patients over 10 years.
Medical factors: Obesity, hypertension, age, sex
Alcohol consumption: A medically recorded variable (but does not capture hidden alcohol use)
HCC development: Target variable

### 🔹 Dataset Size
Number of individuals: 784,308
Number of variables: 325

### 🏥 Dataset Structure

The dataset contains 325 variables, of which 316 are related to 79 diseases.
For each disease, we have two types of diagnosis records:

DP_DR → The main diagnosis, meaning the primary reason for hospitalization.
_ALL → The secondary diagnosis, meaning the disease is present but not necessarily the main cause of hospitalization.

Why 316 variables?
Each of the 79 diseases is associated with two key indicators:

Date of diagnosis
Age at diagnosis

Thus, we have:
79 diseases × 2 indicators × 2 diagnosis types = 316 variables

| **Variable**            | **Type**            | **Description** |
|-------------------------|---------------------|----------------|
| `ID_patient`           | String              | Unique patient identifier |
| `sex`                  | Categorical (binary) | Patient gender |
| `age.min`              | Numeric             | Minimum recorded age during hospitalization |
| `age.max`              | Numeric             | Maximum recorded age during hospitalization |
| `Death`                | Boolean             | Indicates if the patient died during the study period |
| `Diagnosis (79 diseases)` | Categorical       | List of recorded diagnoses |
| `date_diagnosis`       | Date                | Date of each recorded diagnosis |
| `age_at_diagnosis`     | Numeric             | Patient’s age at the time of diagnosis |


## 🎯 Objective

The goal is to build a predictive model that assesses the risk of HCC in patients with T2D, considering their clinical risk factors.

## 🔬 Methodology

#### The study follows a structured data analysis and modeling pipeline:

### 1️⃣ Descriptive Analysis
Initial data exploration: Summary statistics, missing values analysis.
Focus on primary diagnosis (DP_DR): The disease diagnosed as the main reason for hospitalization.
Selection of date variables instead of age:
Dates provide a more precise event timeline.
Age at diagnosis is less specific and more sensitive.
### 📌 Key Decision:
The analysis is centered on primary diagnosis (DP_DR) rather than secondary occurrences, ensuring a focus on the most clinically relevant cases.

### 2️⃣ Data Preprocessing
Re-encoding variables in binary format:
If an explanatory variable event occurred before the target variable (HCC diagnosis) → 1
Otherwise → 0
Handling missing values: Imputation strategies applied as needed.

### 3️⃣ Predictive Modeling
Given the nature of the dataset and the research problem, two models were selected:

LASSO Regression (L1-regularized logistic regression)
Goal: Identify the most important predictors by shrinking coefficients of less relevant features to zero.
Why? LASSO is useful for feature selection in high-dimensional datasets (many variables).
Random Forest Classifier
Goal: Capture complex interactions between predictors.
Why? Handles non-linear relationships and feature importance ranking.
### 📌 Comparison of Model Performance:
Accuracy, precision, recall, and AUC-ROC scores were evaluated.
Feature importance analysis helped identify key risk factors for hepatocellular carcinoma (HCC).

### 📊 Survival Analysis with Cox Proportional Hazards Model

To further investigate predictive factors for hepatocellular carcinoma (HCC), a survival analysis was conducted.

#### 1️⃣ Event Definition & Variable Encoding
Consistent approach with previous preprocessing:
Binary encoding (0 or 1) for explanatory variables.
Ensuring temporal precedence between risk factors and HCC occurrence.
Creation of age_diag (Diagnosis Age) variable:
If the patient developed HCC, age_diag takes the value from age_hepatocellular_carcinoma_dp_dr.
If the patient did not develop HCC, age_diag is set to age.max (i.e., last recorded age in the dataset).
This setup ensures that each individual has a start and end time for observation.

2️⃣ Survival Profiles & Risk Behaviors
To analyze different risk profiles, a categorical variable profil was created:
etat = case_when(
      hepatocellular_carcinoma & !alcohol_use_disorder & !obesity & !smoking ~ "healthy",
      hepatocellular_carcinoma & alcohol_use_disorder & !obesity ~ "alcohol_disorder",
      hepatocellular_carcinoma & !alcohol_use_disorder & obesity ~ "obesity",
      hepatocellular_carcinoma & !alcohol_use_disorder & !obesity & smoking ~ "alcohol_obesity",
      TRUE ~ "0"
    )
#### 3️⃣ Cox Proportional Hazards Model
A Cox regression model was used to estimate the hazard ratio for different risk factors.
#### 📌 Why Cox regression?
It allows estimating the impact of risk factors on survival time.
It accounts for censored data (i.e., patients who did not develop HCC by the end of the study).
It helps quantify the relative contribution of each factor to HCC development.

#### 4️⃣ Results Interpretation
Hazard Ratios (HR) > 1: Increase in risk of HCC.
HR < 1: Protective effect.
Statistical significance (p-value < 0.05) determines whether a factor is clinically relevant.
