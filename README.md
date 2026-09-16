# Outbreak Detection of Heat-Related Illness Under Climate Changes: Development and Evaluation of a Machine Learning-Based Method

## Overview

This repository provides data, trained models, prediction results, and analysis code associated with our study of heat-related illness (HRI) in South Korea.

We analyzed historical HRI cases during 2016–2023 and projected future cases during 2024–2070 under four climate scenarios: SSP1-2.6, SSP2-4.5, SSP3-7.0, and SSP5-8.5.

The shared materials support inspection of model outputs and reproduction of selected analyses and figures presented in the manuscript.

## Repository Structure

- `data/`
  - `observed_hri/`: Regional HRI observations
  - `ssp_scenarios/`: SSP-based meteorological and demographic inputs
- `results/`
  - `historical/`: Historical predictions from each model
  - `future/`: HRI projections under each SSP scenario
  - `parameter_selection/`: Weighting coefficients, hyperparameters, and validation performance
- `models/`: Selected trained models and associated preprocessing information
- `code/`
  - `model_comparison/`: Model performance evaluation and visualization
  - `warning_comparison/`: Comparison of KMA warnings and KDCA-based HRI risk levels
  - `hockey_stick/`: Hockey-stick analysis and visualization
- `requirements.txt`: Package dependencies
- `README.md`: Repository documentation

## Study Design

Four machine-learning models were evaluated:

- Linear regression (LR)
- Random forest (RF)
- Extreme gradient boosting (XGBoost)
- Long short-term memory (LSTM)

The historical dataset was divided chronologically:

| Dataset | Period |
|---|---|
| Training | 2016–2020 |
| Validation | 2021 |
| Test | 2022–2023 |

The models used 18 input features. Model-specific weighting coefficients and hyperparameters were selected using validation performance.

The weighted WBGT index was calculated as:

WBGT_weight,t = WBGT_t + α × WBGT_(t−1) + β × WBGT_(t−2)

Candidate values for α and β ranged from 0.1 to 1.0 in increments of 0.1, subject to α ≥ β, yielding 55 combinations.

## Data

### Observed HRI Data

Historical observations contain regional HRI case counts during each year’s surveillance period.

### SSP Scenario Inputs

Future input data cover 2024–2070 under:

- SSP1-2.6
- SSP2-4.5
- SSP3-7.0
- SSP5-8.5

These inputs include meteorological variables, derived heat-exposure indicators, and regional characteristics.

Regional demographic projections were used through 2052. Values for 2053–2070 were extrapolated using region-specific linear trends estimated from the 2043–2052 projections. The baseline applied the full estimated trends.

Sensitivity analyses under SSP2-4.5 varied the post-2052 slopes for total population, population aged ≥60 years, and the proportion of older adults living alone by −20%, −10%, +10%, and +20%.

## Results

### Historical Model Predictions

Historical results include observed and predicted HRI cases for each model, with training, validation, and test periods identified separately.

| Field | Description |
|---|---|
| `region` | Study region |
| `date` | Date corresponding to the observation and prediction |
| `observed` | Observed number of HRI cases |
| `predicted` | Model-predicted number of HRI cases |
| `split` or `period` | Training, validation, or test period |

Predictions are continuous-valued outputs and are not rounded to integer counts. Prediction dates may differ from the full surveillance period because consecutive input observations are required.

### Future HRI Projections

Future results contain LSTM-based HRI projections for each SSP scenario during 2024–2070.

These outputs are distinct from the SSP input data stored in `data/ssp_scenarios/`.

### Parameter Selection

Parameter-selection results document model-specific α and β values, selected hyperparameters, and validation performance.

Where repeated-seed results are provided, individual-run performance and summary statistics are identified separately.

## Trained Models

The `models/` directory contains the selected trained models.

When using these models:

- Preserve the input feature names and order.
- Apply the preprocessing and normalization used during training.
- Use the model-specific α and β values when calculating weighted WBGT.
- Preserve the sequence length required by the model.
- Set the LSTM model to evaluation mode before prediction.

Small numerical differences may occur across software versions and CPU/GPU environments.

## Analysis Code

### Model Comparison

Code for evaluating model performance and plotting observed and predicted HRI cases.

### Warning Comparison

Code for comparing KMA heatwave warnings with HRI risk levels classified using KDCA criteria.

KDCA-based classifications generated in this study should not be interpreted as official forecasts or operational warnings issued by KDCA.

### Hockey-Stick Analysis

Code for examining and visualizing the relationship between heat exposure and HRI cases using the hockey-stick model.

## Usage

1. Install the dependencies listed in `requirements.txt`.
2. Retain the repository directory structure.
3. Open the relevant notebook or script in `code/`.
4. Check its input and output paths.
5. Run the analysis using the provided data, model files, or prediction results.

The repository provides trained models and result-analysis code. It does not include the complete model-training and hyperparameter-search pipeline.

## Data Sources

The study used information from:

- **Korea Disease Control and Prevention Agency (KDCA):** HRI surveillance
- **Korea Meteorological Administration (KMA):** meteorological data, heatwave warnings, and SSP climate projections
- **Korean Statistical Information Service (KOSIS):** regional demographic and socioeconomic data

Source data remain subject to the applicable terms of their original providers.

## Interpretation

Future HRI projections are conditional on the climate scenarios, demographic assumptions, and fitted model.

The shared models and outputs are intended for research and should not be used as a substitute for official public health guidance or warning systems.

## Citation

**Outbreak Detection of Heat-Related Illness Under Climate Changes: Development and Evaluation of a Machine Learning-Based Method**

Full publication details and the DOI will be added when available.
