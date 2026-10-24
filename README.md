# Soil Thermal Conductivity Prediction and Explainability

A machine learning-based application for estimating soil thermal conductivity using a trained Gradient Boosting Regression model, a proposed analytical equation, and SHAP-based model interpretation.

## Overview

Soil thermal conductivity is an important thermophysical property governing heat transfer in soil and is relevant to geotechnical engineering, geothermal energy systems, and other soil–energy applications. Its estimation is challenging because it depends on several interacting physical and compositional properties of the soil.

This repository provides a computational framework for predicting soil thermal conductivity using a trained machine learning model and a proposed quadratic analytical equation. An interactive web application developed with Streamlit enables users to enter soil properties, compare predictions from the two approaches, and interpret individual machine learning predictions using SHapley Additive exPlanations (SHAP).

The application is intended to support research, model interpretation, and preliminary computational assessment.

## Key Features

- **Machine learning prediction:** Estimates soil thermal conductivity using a saved Gradient Boosting Regression pipeline.
- **Analytical prediction:** Evaluates a proposed quadratic equation using porosity, saturation degree, and quartz content.
- **Explainable artificial intelligence:** Generates SHAP waterfall plots to illustrate the contributions of input features to individual machine learning predictions.
- **Prediction comparison:** Reports the machine learning and analytical estimates, together with their absolute difference and signed difference.
- **Interactive interface:** Provides a Streamlit-based interface for entering soil properties and examining results.
- **Reference dataset analysis:** Displays feature ranges and summary statistics from the reference dataset used by the application.

## Methodology

### 1. Input Features

The machine learning model uses six soil properties as input variables.

| Input feature | Description |
|---|---|
| Dry density | Dry density of the soil |
| Porosity | Fraction or proportion of the soil volume occupied by voids |
| Saturation degree | Degree of saturation of the soil |
| Quartz content | Quartz fraction or proportion |
| Sand content | Sand fraction or proportion |
| Clay content | Clay fraction or proportion |

**Important:** Input units and numerical scales must be consistent with those used in the original training dataset. For example, fractions and percentages are not interchangeable.

### 2. Machine Learning Model

The application loads a previously trained and serialized machine learning pipeline from `best_model.pkl`. The pipeline includes preprocessing steps and a Gradient Boosting Regressor.

Given an input vector

\[
\mathbf{x} =
[\rho_d,n,S,Q,C_s,C_c]
\]

the model estimates thermal conductivity as

\[
\hat{K}_{\mathrm{ML}}=f_{\mathrm{GBR}}(\mathbf{x})
\]

where \(f_{\mathrm{GBR}}\) denotes the trained Gradient Boosting pipeline, and the input variables correspond to the six soil properties listed above.

The model is loaded from the saved artifact; the application does not retrain the estimator when a prediction is requested.

### 3. Analytical Equation

In addition to the machine learning model, the application implements the following quadratic equation:

\[
\begin{aligned}
K_{\mathrm{eq}}={}&2.740221-0.598635Q\\
&+3.078759S-8.672656n\\
&+1.399861Q^2-1.991327S^2\\
&+6.718210n^2
\end{aligned}
\]

where:

- \(K_{\mathrm{eq}}\) is the estimated thermal conductivity.
- \(Q\) is quartz content.
- \(S\) is degree of saturation.
- \(n\) is porosity.

The equation uses three input variables: quartz content, degree of saturation, and porosity. Dry density, sand content, and clay content are not included explicitly.

The variables must be entered on the same numerical scales used to develop the equation. The thermal conductivity unit and the equation's calibration domain should be verified against the corresponding research study before the equation is applied to new datasets.

### 4. SHAP-Based Model Interpretation

The application uses SHAP with a tree-based explainer to interpret individual machine learning predictions. A SHAP waterfall plot illustrates how the contributions of individual features move the prediction away from the reference or baseline output.

The visualization supports the interpretation of:

- **Baseline value:** The reference output determined by the SHAP background dataset.
- **Positive SHAP contributions:** Feature contributions that increase the prediction relative to the baseline.
- **Negative SHAP contributions:** Feature contributions that decrease the prediction relative to the baseline.
- **Final prediction:** The baseline combined with the individual SHAP contributions.

SHAP explanations are model-dependent and should not be interpreted as proof of causal relationships between soil properties and thermal conductivity.

## Repository Structure

```text
Thermal-Conductivity/
├── app.py
├── best_model.pkl
├── Data.xlsx
├── requirements.txt
└── README.md
```

| File | Purpose |
|---|---|
| `app.py` | Implements the Streamlit application, prediction workflow, analytical equation, and SHAP visualization. |
| `best_model.pkl` | Contains the serialized, trained machine learning pipeline. |
| `Data.xlsx` | Provides the default reference dataset used for the SHAP background and feature statistics. |
| `requirements.txt` | Specifies the principal Python dependencies and their versions where pinned. |
| `README.md` | Documents the project, methodology, installation, and use. |

## Installation

### Prerequisites

- Python 3.11 is the recommended environment for this project.
- Git
- Access to the repository files and required Python packages

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

Upgrade pip and install the packages listed in `requirements.txt`:

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

The principal dependencies include PyCaret, scikit-learn, NumPy, pandas, Joblib, Streamlit, SHAP, Matplotlib, and OpenPyXL.

For reproducible execution, use compatible package versions and the same preprocessing and model environment used to create `best_model.pkl`.

## Running the Application

From the repository directory, execute:

```bash
streamlit run app.py
```

Streamlit will provide a local URL in the terminal, typically:

```text
http://localhost:8501
```

Open this address in a web browser to access the application.

## Application Workflow

1. **Provide the soil properties.** Enter dry density, porosity, saturation degree, quartz content, sand content, and clay content using the appropriate units and numerical scales.
2. **Generate predictions.** Select *Predict and Explain* to calculate the machine learning and analytical estimates.
3. **Compare the estimates.** Examine the predicted values and the difference between the two approaches.
4. **Interpret the machine learning output.** Inspect the SHAP waterfall plot to understand the feature contributions for the selected input sample.
5. **Review the reference dataset.** Examine the reported feature ranges and summary statistics to assess whether the input values fall within the observed data domain.

The application initializes its example inputs using the medians of the reference dataset. These initial values are provided for convenience and should not be treated as recommended design values.

## Dataset Requirements

By default, the application expects `Data.xlsx` in the same directory as `app.py`. An alternative Excel file can be uploaded through the application's dataset interface.

The reference dataset must contain the following columns with matching names:

```text
Dry density
Porosity
Saturation degree
Quartz content
Sand content
Clay content
```

The uploaded dataset is used as the SHAP reference dataset and for reporting feature statistics. It does not retrain the saved machine learning model.

Consequently, a dataset with the required column names is not necessarily suitable for explaining the model. Its feature distributions and numerical scales should be consistent with the model's training data.

## Reproducibility and Scientific Considerations

For research applications, the following considerations are important:

- **Model provenance:** `best_model.pkl` must correspond to the intended trained model and preprocessing pipeline.
- **Data consistency:** Input variables must use the same units, definitions, and numerical scales as those used during model development.
- **Domain of applicability:** Predictions outside the training-data ranges may be unreliable and should be assessed carefully.
- **Model interpretation:** SHAP describes the behavior of the trained model for a particular sample and reference dataset; it does not establish physical causation.
- **Independent validation:** The application itself does not provide new experimental validation of the predictions. Model accuracy must be established using the evaluation procedures and independent data reported in the underlying research.
- **Equation limitations:** The analytical equation should be applied within its documented calibration domain, with its original units and variable definitions preserved.
- **Prediction differences:** The difference between the machine learning and analytical estimates is a comparison between two predictions, not an error relative to an experimental measurement.

For publication-quality reproducibility, the associated research manuscript should document the dataset source, sample size, model development procedure, validation strategy, evaluation metrics, and equation derivation.

## Intended Use

This repository is intended for academic research, methodological demonstration, and preliminary estimation of soil thermal conductivity.

Predictions should not be used as a substitute for experimental measurements, independent validation, or engineering judgment where reliable thermal-property estimates are required for design or safety-critical decisions.

## Citation

If this software or its methodology is used in a research publication, please cite the corresponding peer-reviewed article or research work associated with the model and analytical equation.

**Publication reference:** To be added when the relevant manuscript is finalized or published.

## Acknowledgment

This project is associated with research activities in machine learning applications for geotechnical engineering and soil thermal-property estimation.

**Affiliation:** Faculty of Civil, Water and Environmental Engineering, Shahid Beheshti University.

## Copyright

© Dr. Mohammadnezhad's Research Team. All rights reserved.

The software, model artifacts, and associated materials are provided for academic and research purposes. Reuse, redistribution, or modification should follow the applicable repository license and the terms specified by the authors. No particular open-source license is implied by this copyright statement.
