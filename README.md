# Outbreak Detection of Heat-Related Illness Under Climate Changes: Development and Evaluation of a Machine Learning-Based Method

## Overview

This repository provides selected data, model outputs, and visualization code associated with this study.

We analyzed heat-related illness (HRI) in South Korea during 2016–2023 and projected HRI cases for 2024–2070 under four climate scenarios: SSP1-2.6, SSP2-4.5, SSP3-7.0, and SSP5-8.5.

The shared materials support reproduction of selected figures reported in the manuscript.

## Study Design

We evaluated four machine-learning models:

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

Model-specific weighting coefficients and hyperparameters were selected using validation performance. The LSTM model was used to project future HRI cases under the four SSP scenarios.

Regional demographic projections were used through 2052. Values for 2053–2070 were extrapolated using region-specific linear trends estimated from the 2043–2052 projections. Sensitivity analyses under SSP2-4.5 evaluated changes in these demographic trend slopes.

## Shared Materials

### Historical Observations and Model Predictions

Regional HRI observations and corresponding model predictions are provided for the training, validation, and test periods.

| Field | Description |
|---|---|
| `region` | Study region |
| `date` | Date of the observed and predicted cases |
| `observed` | Observed number of HRI cases |
| `predicted` | Model-predicted number of HRI cases |
| `split` or `period` | Training, validation, or test period |

Predictions are continuous-valued model outputs and are not rounded to integer counts. Dates with predictions may differ from the full surveillance period because the models require consecutive input observations.

### Future Projections

Model-generated HRI projections cover 2024–2070 under:

- SSP1-2.6
- SSP2-4.5
- SSP3-7.0
- SSP5-8.5

These projections are conditional on the specified climate scenarios and regional demographic assumptions.

### Visualization Code

Selected scripts or notebooks reproduce:

- Comparisons between observed and predicted HRI cases
- Future HRI projections under the SSP scenarios
- Comparisons of KMA heatwave warnings and HRI-based warning periods

## Scope of Reproducibility

This repository focuses on reproducing selected figures from shared observations and previously generated model outputs.

The complete predictor dataset, full model-training and hyperparameter-search pipeline, and trained model checkpoints are not included. Model retraining is not required to use the visualization code.

## Data Sources

The study used information from:

- **Korea Disease Control and Prevention Agency (KDCA):** HRI surveillance
- **Korea Meteorological Administration (KMA):** meteorological data, heatwave warnings, and SSP climate projections
- **Korean Statistical Information Service (KOSIS):** regional demographic and socioeconomic data

The shared files contain selected study data and processed outputs rather than the complete source datasets.

## Interpretation

Historical predictions describe model performance during the specified evaluation periods. Future projections represent scenario-dependent estimates rather than certain predictions of future case counts.

HRI-based warning categories used in this study should not be interpreted as official forecasts or operational warnings issued by KMA or KDCA.

## Citation

**Outbreak Detection of Heat-Related Illness Under Climate Changes: Development and Evaluation of a Machine Learning-Based Method**

Full publication details and the DOI will be added when available.
