# Soil Thermal Conductivity Prediction and Explainability

A machine learning-based application for estimating soil thermal conductivity using a trained Gradient Boosting Regression model, a quadratic analytical equation, and SHAP-based model interpretation.

## Overview

Soil thermal conductivity is an important thermophysical property governing heat transfer through soil. Its accurate estimation is relevant to geotechnical engineering, geothermal energy systems, and other soil–energy applications. However, predicting this property remains challenging because it depends on several interacting physical and compositional characteristics of the soil.

This repository provides an interactive computational framework for estimating soil thermal conductivity using two approaches: a trained machine learning model and a quadratic analytical equation. The application is developed using Streamlit and incorporates SHapley Additive exPlanations (SHAP) to support the interpretation of individual machine learning predictions.

Users can enter soil properties, compare predictions obtained from the two approaches, and examine the contributions of individual input features to the machine learning output.

## Key Features

- **Machine learning prediction:** Estimates soil thermal conductivity using a pretrained Gradient Boosting Regression pipeline.
- **Analytical estimation:** Calculates thermal conductivity using a quadratic equation involving porosity, degree of saturation, and quartz content.
- **Explainable artificial intelligence:** Uses SHAP waterfall plots to examine feature contributions to individual machine learning predictions.
- **Prediction comparison:** Presents the estimates obtained from both approaches and their numerical differences.
- **Interactive interface:** Provides a browser-based environment for entering soil properties and examining results.
- **Reference dataset analysis:** Reports descriptive statistics and feature ranges from the reference dataset.

## Methodology

### 1. Input Features

The machine learning model uses six input variables describing the physical state and composition of the soil.

| Symbol | Feature | Engineering definition | Unit or representation |
|---|---|---|---|
| ρd | Dry density | Mass of dry soil solids per unit of total soil volume | kg/m³ or Mg/m³ |
| n | Porosity | Ratio of void volume to total soil volume | Dimensionless ratio or percentage |
| S | Degree of saturation | Ratio of water volume to void volume | Dimensionless ratio or percentage |
| Q | Quartz content | Proportion of the soil's solid mineral composition represented by quartz | Fraction or percentage |
| Cs | Sand content | Proportion of the soil classified as sand | Fraction or percentage |
| Cc | Clay content | Proportion of the soil classified as clay | Fraction or percentage |

The input vector is defined as

$$
\mathbf{x} = [\rho_d, n, S, Q, C_s, C_c]
$$

The units, numerical scales, and definitions of the input variables must be consistent with those used during model development. For example, a porosity value of 0.35 is numerically different from 35, even though both may represent the same physical porosity when expressed as a fraction and a percentage, respectively.

The original dataset and model-development procedure should be consulted to verify the exact units and conventions.

### 2. Machine Learning Model

The application loads a previously trained machine learning pipeline from `best_model.pkl`. The pipeline incorporates preprocessing operations and a Gradient Boosting Regressor.

The predicted thermal conductivity is expressed as

$$
\hat{K}_{\mathrm{ML}} = f_{\mathrm{GBR}}(\mathbf{x})
$$

where:

- **K̂ML** denotes the thermal conductivity predicted by the machine learning model.
- **fGBR** denotes the trained Gradient Boosting Regression pipeline.
- **x** denotes the input vector containing the six soil properties.

The serialized pipeline is loaded for inference. The model is not retrained whenever a new input sample is evaluated.

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

- **Keq** denotes the thermal conductivity estimated using the analytical equation.
- **Q** represents quartz content.
- **S** represents degree of saturation.
- **n** represents porosity.

Unlike the machine learning model, the analytical equation explicitly uses only three input variables: quartz content, degree of saturation, and porosity.

The equation must be evaluated using the original variable definitions, numerical scales, and thermal conductivity units under which its coefficients were developed. Its applicability to new soil types should be assessed against the calibration domain of the underlying research.

### 4. SHAP-Based Model Interpretation

The application uses SHAP with a tree-based explainer to interpret individual machine learning predictions.

A model prediction can be decomposed into a baseline value and the contributions attributed to individual input features:

$$
\hat{K}_{\mathrm{ML}} = E[f(\mathbf{X})] + \sum_{i=1}^{6}\phi_i
$$

where:

- **E[f(X)]** represents the baseline model output, calculated with respect to the SHAP background distribution.
- **φᵢ** represents the SHAP value assigned to feature i, quantifying its contribution to the difference between the explained prediction and the baseline.
- **K̂ML** denotes the final machine learning prediction for the sample being explained.
- **i** indexes the six input features.

The SHAP waterfall plot illustrates how the contributions of individual features combine to move the prediction from its baseline value to the final model output.

A positive SHAP value indicates that a feature contributes to increasing the prediction relative to the baseline, whereas a negative SHAP value indicates a contribution that decreases the prediction relative to the baseline.

SHAP values explain the behavior of the trained model for a specified input and background distribution. They should not be interpreted as evidence of causal relationships between soil properties and thermal conductivity.

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
| `app.py` | Implements the Streamlit interface, prediction workflow, analytical equation, and SHAP visualization. |
| `best_model.pkl` | Contains the serialized, trained machine learning pipeline. |
| `Data.xlsx` | Contains the reference dataset used by the application. |
| `requirements.txt` | Specifies the Python dependencies required to run the application. |
| `README.md` | Documents the project's objectives, methodology, installation, and use. |

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

For reproducible execution, use package versions compatible with the saved model and its preprocessing pipeline.

## Running the Application

From the repository directory, execute:

```bash
streamlit run app.py
```

Streamlit will display a local URL in the terminal, typically:

`http://localhost:8501`

Open the URL in a web browser to access the application.

## Application Workflow

1. **Enter soil properties.** Provide dry density, porosity, degree of saturation, quartz content, sand content, and clay content using the required units and numerical scales.
2. **Generate predictions.** Select *Predict and Explain* to calculate the machine learning and analytical estimates.
3. **Compare estimates.** Examine the predicted values and their numerical differences.
4. **Interpret the machine learning prediction.** Review the SHAP waterfall plot to investigate the contributions of individual features.
5. **Review the reference dataset.** Examine feature ranges and descriptive statistics to assess whether the input values are representative of the available data.

The application initializes example inputs using the medians of the reference dataset. These initial values are provided for convenience and should not be interpreted as recommended engineering parameters.

## Dataset Requirements

By default, the application expects `Data.xlsx` in the same directory as `app.py`. An alternative Excel dataset can be uploaded through the application's dataset interface, where supported.

The reference dataset is expected to contain the following feature columns with matching names:

```text
Dry density
Porosity
Saturation degree
Quartz content
Sand content
Clay content
```

The reference dataset supports descriptive statistics and SHAP interpretation. Uploading a new dataset does not retrain the saved machine learning model.

For meaningful interpretation, the reference dataset should be consistent with the model's training data in terms of variable definitions, numerical scales, and feature distributions. Matching column names alone does not guarantee that the dataset is suitable for explaining the model.

## Reproducibility and Scientific Considerations

The following considerations are important when applying this framework in academic research:

- **Model provenance:** The saved artifact must correspond to the intended trained estimator and preprocessing pipeline.
- **Input consistency:** Variable definitions, measurement units, and numerical scales must match those used during model development.
- **Domain of applicability:** Predictions outside the training-data ranges may be unreliable and should be evaluated cautiously.
- **Model interpretation:** SHAP explains the behavior of the trained model but does not establish physical causality.
- **Independent validation:** The application does not independently validate predictions against new experimental measurements. Predictive performance should be established using appropriate validation procedures and independent data.
- **Analytical equation limitations:** The equation should only be applied within its documented calibration domain and with its original variable definitions and units.
- **Prediction differences:** Disagreement between the two approaches is not equivalent to prediction error relative to an experimental measurement.

For publication-quality reproducibility, the associated research paper should document the dataset source, sample size, data preprocessing, model-development procedure, validation strategy, evaluation metrics, and derivation or calibration of the analytical equation.

## Intended Use

This repository is intended for academic research, methodological demonstration, and preliminary estimation of soil thermal conductivity.

Its predictions should not replace experimental measurements, independent validation, or professional engineering judgment when reliable thermal-property estimates are required for engineering design or safety-critical applications.

## Citation

If this software or its methodology is used in an academic publication, please cite the relevant research article associated with the model and analytical equation.

**Publication reference:** To be added when the corresponding research work is finalized or published.

## Acknowledgment

This project is associated with research activities in machine learning applications for geotechnical engineering and soil thermal-property estimation.

**Affiliation:** Faculty of Civil, Water and Environmental Engineering, Shahid Beheshti University.

## Copyright

© Dr. Mohammadnezhad's Research Team. All rights reserved.

The software, trained model artifacts, and associated materials are provided for academic and research purposes. Reuse, redistribution, and modification are subject to the applicable repository license and the terms specified by the authors. This copyright statement does not itself establish an open-source license.
