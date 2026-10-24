# Soil Thermal Conductivity Prediction and Explainability

A machine learning-based application for estimating soil thermal conductivity using a trained Gradient Boosting Regression model, a quadratic analytical equation, and SHAP-based model interpretation.

## Overview

Soil thermal conductivity is an important thermophysical property governing heat transfer in soil. Its accurate estimation is relevant to geotechnical engineering, geothermal energy systems, and other soil–energy applications. However, prediction remains challenging because thermal conductivity depends on multiple interacting soil properties.

This repository provides an interactive computational framework for estimating soil thermal conductivity using two approaches: a trained machine learning model and a quadratic analytical equation. The application is developed using Streamlit and incorporates SHapley Additive exPlanations (SHAP) to support the interpretation of individual machine learning predictions.

The framework enables users to enter soil properties, compare the predictions obtained from the two approaches, and investigate the contributions of individual input features to the machine learning output.

## Key Features

- **Machine learning prediction:** Estimates soil thermal conductivity using a pretrained Gradient Boosting Regression pipeline.
- **Analytical estimation:** Calculates thermal conductivity using a quadratic equation involving porosity, degree of saturation, and quartz content.
- **Explainable artificial intelligence:** Uses SHAP waterfall plots to examine feature contributions to individual machine learning predictions.
- **Prediction comparison:** Presents machine learning and analytical estimates and their numerical differences.
- **Interactive interface:** Provides a browser-based application for entering input parameters and examining results.
- **Reference dataset analysis:** Reports descriptive statistics and feature ranges from the reference dataset.

## Methodology

### 1. Input Features

The machine learning model uses six soil properties as input variables.

| Symbol | Input feature | Description |
|---|---|---|
| \(\rho_d\) | Dry density | Dry density of the soil |
| \(n\) | Porosity | Fraction of the total soil volume occupied by voids |
| \(S\) | Degree of saturation | Fraction of the void space occupied by water |
| \(Q\) | Quartz content | Quartz fraction or proportion |
| \(C_s\) | Sand content | Sand fraction or proportion |
| \(C_c\) | Clay content | Clay fraction or proportion |

The input vector is defined as

$$
\mathbf{x} = [\rho_d, n, S, Q, C_s, C_c]
$$

Input values must use the same units, numerical scales, and variable definitions as those used during model development.

### 2. Machine Learning Model

The application loads a previously trained machine learning pipeline from `best_model.pkl`. The pipeline incorporates preprocessing steps and a Gradient Boosting Regressor.

The predicted thermal conductivity is expressed as

$$
\hat{K}_{\mathrm{ML}} = f_{\mathrm{GBR}}(\mathbf{x})
$$

where:

- \(\hat{K}_{\mathrm{ML}}\) denotes the machine learning prediction.
- \(f_{\mathrm{GBR}}\) represents the trained Gradient Boosting Regression pipeline.
- \(\mathbf{x}\) represents the vector of six input features.

The serialized pipeline is loaded for inference; the model is not retrained whenever a prediction is generated.

### 3. Analytical Equation

The application also implements the following quadratic equation for estimating soil thermal conductivity:

$$
\begin{aligned}
K_{\mathrm{eq}} ={}& 2.740221 - 0.598635Q \\
&+ 3.078759S - 8.672656n \\
&+ 1.399861Q^2 - 1.991327S^2 \\
&+ 6.718210n^2
\end{aligned}
$$

where:

- \(K_{\mathrm{eq}}\) denotes the analytical estimate of thermal conductivity.
- \(Q\) represents quartz content.
- \(S\) represents degree of saturation.
- \(n\) represents porosity.

Unlike the machine learning model, the analytical equation explicitly uses only three input variables: quartz content, degree of saturation, and porosity.

The equation must be evaluated using the original variable definitions, numerical scales, and thermal conductivity units under which its coefficients were developed. Its applicability to new soils should be assessed against the original calibration domain.

### 4. SHAP-Based Model Interpretation

The application employs SHAP with a tree-based explainer to interpret individual machine learning predictions.

SHAP decomposes a prediction into a baseline value and feature contributions:

$$
\hat{K}_{\mathrm{ML}} = E[f(\mathbf{x})] + \sum_{i=1}^{6}\phi_i
$$

where:

- \(E[f(\mathbf{x})]\) represents the reference or baseline model output.
- \(\phi_i\) represents the SHAP contribution associated with feature \(i\).
- \(\hat{K}_{\mathrm{ML}}\) denotes the final model prediction.

The SHAP waterfall plot illustrates how individual features contribute to the difference between the baseline and the prediction for a selected sample.

Positive SHAP values indicate contributions that increase the prediction relative to the baseline, whereas negative SHAP values indicate contributions that decrease it.

SHAP values describe the behavior of the trained model for the specified input and reference dataset. They should not be interpreted as evidence of causal relationships between soil properties and thermal conductivity.

## Repository Structure

```text
Thermal-Conductivity/
├── app.py
├── best_model.pkl
├── Data.xlsx
├── requirements.txt
└── README.md
```

| File | Description |
|---|---|
| `app.py` | Implements the Streamlit interface, prediction calculations, analytical equation, and SHAP visualization. |
| `best_model.pkl` | Contains the serialized, trained machine learning pipeline. |
| `Data.xlsx` | Contains the reference dataset used by the application. |
| `requirements.txt` | Specifies the Python dependencies required to run the application. |
| `README.md` | Provides documentation for the project and its methodology. |

## Installation

### Prerequisites

- Python 3.11 is the recommended environment.
- Git
- A compatible Python package environment.
- Access to the repository files and required dependencies.

### 1. Clone the Repository

```bash
git clone https://github.com/HamidMohammadnezhad/Thermal-Conductivity.git
cd Thermal-Conductivity
```

### 2. Create a Virtual Environment

```bash
python -m venv .venv
```

Activate the environment.

**Windows**

```bash
.venv\Scripts\activate
```

**Linux or macOS**

```bash
source .venv/bin/activate
```

### 3. Install Dependencies

Install the required packages using the provided dependency file:

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

The application relies on packages for machine learning, data processing, model interpretation, visualization, and web application development, including PyCaret, scikit-learn, NumPy, pandas, Joblib, Streamlit, SHAP, Matplotlib, and OpenPyXL.

For reproducible execution, use package versions compatible with the serialized model and its preprocessing pipeline.

## Running the Application

From the repository directory, execute:

```bash
streamlit run app.py
```

Streamlit will display a local URL in the terminal, typically:

`http://localhost:8501`

Open the URL in a web browser to access the application.

## Application Workflow

1. **Enter soil properties.** Provide the six input parameters using the required units and numerical scales.
2. **Generate predictions.** Select *Predict and Explain* to obtain the machine learning and analytical estimates.
3. **Compare the estimates.** Examine the predicted values and their numerical differences.
4. **Interpret the machine learning prediction.** Review the SHAP waterfall plot to investigate the contributions of individual input features.
5. **Review the reference dataset.** Examine the feature ranges and descriptive statistics to assess the representativeness of the selected input values.

The application initializes example inputs using the medians of the reference dataset. These initial values are provided for convenience and should not be interpreted as recommended engineering parameters.

## Dataset Requirements

By default, the application expects `Data.xlsx` in the same directory as `app.py`. An alternative Excel dataset can be uploaded through the application's dataset interface, where supported.

The reference dataset must contain the following feature columns with matching names:

```text
Dry density
Porosity
Saturation degree
Quartz content
Sand content
Clay content
```

The dataset is used to provide reference statistics and support SHAP interpretation. Uploading a new dataset does not retrain the serialized machine learning model.

For meaningful interpretation, the reference dataset should be consistent with the model's training data in terms of variable definitions, numerical scales, and feature distributions. Merely matching the required column names is not sufficient to guarantee valid explanations.

## Reproducibility and Scientific Considerations

The following considerations are important when applying this framework in research:

- **Model provenance:** The saved model must correspond to the intended trained estimator and preprocessing pipeline.
- **Input consistency:** Variable definitions, units, and numerical scales must match those used during model development.
- **Domain of applicability:** Predictions outside the training-data ranges may be unreliable and should be evaluated cautiously.
- **Model interpretability:** SHAP explains the trained model's predictions but does not establish physical causality.
- **Independent validation:** The application does not independently validate its predictions against new experimental measurements. Predictive performance should be established using appropriate validation procedures and independent data.
- **Analytical equation limitations:** The quadratic equation should only be applied within its documented calibration domain.
- **Prediction differences:** Differences
