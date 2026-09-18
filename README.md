# Outbreak Detection of Heat-Related Illness Under Climate Changes: Development and Evaluation of a Machine Learning-Based Method

## Overview

This repository provides data, trained models, prediction results, and analysis code associated with our study of heat-related illness (HRI) in South Korea.

We analyzed historical HRI cases during 2016–2023 and projected future cases during 2024–2070 under four climate scenarios: SSP1-2.6, SSP2-4.5, SSP3-7.0, and SSP5-8.5.

## Repository Structure

```text
Heat-risk-warning/
├── README.md
├── requirements.txt
├── previous/                  # Archived files from the earlier repository version
├── code/
│   ├── Model_comparison.ipynb
│   ├── Warning_comparison.ipynb
│   └── Hockey_stick.ipynb
├── data/
│   ├── Final_heat_region.xlsx
│   ├── Final_heat_total.xlsx
│   ├── Final_SSP126.csv
│   ├── Final_SSP245.csv
│   ├── Final_SSP370.csv
│   └── Final_SSP585.csv
├── models/
│   ├── LR_model.pkl
│   ├── RF_model.pkl
│   ├── XGB_model.json
│   └── LSTM_model.pth
├── results/
│   ├── Model_result.xlsx
│   ├── ML_parameter_selection.csv
│   ├── LSTM_parameter_selection.csv
│   ├── SSP126_result.csv
│   ├── SSP245_result.csv
│   ├── SSP370_result.csv
│   └── SSP585_result.csv
└── figure/
    ├── Model_comparison_national.jpg
    ├── Model_comparison_regional.jpg
    ├── warning_comparison_national.jpg
    ├── warning_comparison_regional.jpg
    ├── Hockey_stick1.jpg
    └── Hockey_stick2.jpg
```

## Study Design

Four machine-learning models were evaluated: linear regression (LR), random forest (RF), extreme gradient boosting (XGBoost), and long short-term memory (LSTM).

The historical dataset was divided chronologically:

| Dataset | Period |
|---|---|
| Training | 2016–2020 |
| Validation | 2021 |
| Test | 2022–2023 |

The models used 18 input features. Model-specific weighting coefficients and hyperparameters were selected using validation performance.

The weighted WBGT index was calculated as:

$WBGT_{weight,t} = WBGT_{t} + \alpha × WBGT_{t−1} + \beta × WBGT_{t−2}$

Candidate values for $\alpha$ and $\beta$ ranged from 0.1 to 1.0 in increments of 0.1.

## Data

### Observed HRI Data

[Final_heat_region.xlsx](data/Final_heat_region.xlsx) contains regional HRI case counts, meteorological variables, and regional characteristics for each year's HRI surveillance period. The 18 predictors are selected from this workbook by the model-comparison notebook.

### SSP Scenario Inputs

Future input data cover 2024–2070 and include meteorological variables, derived heat-exposure indicators, and regional characteristics.

| Scenario | Input data | Predicted HRI cases |
|---|---|---|
| SSP1-2.6 | [Final_SSP126.csv](data/Final_SSP126.csv) | [SSP126_result.csv](results/SSP126_result.csv) |
| SSP2-4.5 | [Final_SSP245.csv](data/Final_SSP245.csv) | [SSP245_result.csv](results/SSP245_result.csv) |
| SSP3-7.0 | [Final_SSP370.csv](data/Final_SSP370.csv) | [SSP370_result.csv](results/SSP370_result.csv) |
| SSP5-8.5 | [Final_SSP585.csv](data/Final_SSP585.csv) | [SSP585_result.csv](results/SSP585_result.csv) |

Regional demographic projections were used through 2052. Values for 2053–2070 were extrapolated using region-specific linear trends estimated from the 2043–2052 projections.

## Results

### Historical Model Predictions

[Model_result.xlsx](results/Model_result.xlsx) contains four sheets — `LR`, `RF`, `XGB`, and `LSTM` — with the following fields:

| Field | Description |
|---|---|
| `region` | Study region |
| `date` | Date corresponding to the observation and prediction |
| `observed` | Observed number of HRI cases |
| `predicted` | Model-predicted number of HRI cases |
| `period` | `train`, `validation`, or `test` |

Predictions are continuous-valued and are not rounded to integer counts. Prediction dates may differ from the full surveillance period because consecutive input observations are required.

### Future HRI Projections

Each `SSP*_result.csv` holds LSTM-based projections for 2024–2070 in wide format: `date`, one prediction column per region, `year`, `Korea` (the summed prediction across study regions), and `date2` (month-day).

### Parameter Selection

| File | Contents |
|---|---|
| [ML_parameter_selection.csv](results/ML_parameter_selection.csv) | LR, RF, and XGBoost results by α/β combination, including validation/test MSE, RF/XGBoost hyperparameters, and run settings. |
| [LSTM_parameter_selection.csv](results/LSTM_parameter_selection.csv) | LSTM hyperparameters and repeated-seed validation summaries by α/β combination (mean, median, standard deviation, minimum, maximum, ranking). |

Parameter selection used validation metrics only; test metrics are reported separately. `mean_valid_mse` in the LSTM summary is an average across seed runs, not the validation MSE of a single saved checkpoint.

## Trained Models

| File | Model | Loading method |
|---|---|---|
| `LR_model.pkl` | Linear regression | `joblib.load` |
| `RF_model.pkl` | Random forest | `joblib.load` |
| `XGB_model.json` | XGBoost | `XGBRegressor.load_model` |
| `LSTM_model.pth` | LSTM | `torch.load`, followed by `load_state_dict` |

The LSTM checkpoint includes weights, hyperparameters, feature order, sequence length, training/validation years, and feature-scaling values. The model-comparison notebook reconstructs LR/RF/XGBoost scaling from the historical training data.

When using these models, preserve the input feature names and order, apply the model-specific α and β values when calculating weighted WBGT, and keep the sequence length required by the LSTM.

## Analysis Code

### Model Comparison

[Model_comparison.ipynb](code/Model_comparison.ipynb) loads `data/Final_heat_region.xlsx` and the four saved models. It calculates training, validation, and test metrics, aggregates regional predictions to national totals, and plots national and regional observed–predicted comparisons. It does not retrain the models.

Figures: `figure/Model_comparison_national.jpg`, `figure/Model_comparison_regional.jpg`.

### Warning Comparison

[Warning_comparison.ipynb](code/Warning_comparison.ipynb) uses `data/Final_heat_region.xlsx` and the `LSTM` sheet of `results/Model_result.xlsx` to compare warning periods during 2022–2023. It computes KMA-related warning indicators from WBGT-derived apparent temperature — calculated in the notebook rather than loaded from an official warning-issuance log — and applies KDCA-based HRI risk levels to observed and predicted case counts.

Figures: `figure/warning_comparison_national.jpg`, `figure/warning_comparison_regional.jpg`.

### Hockey-Stick Analysis

[Hockey_stick.ipynb](code/Hockey_stick.ipynb) reads `data/Final_heat_region.xlsx`, fits regional WBGT–HRI relationships using a Poisson hockey-stick model, and calculates approximate confidence intervals using parametric draws. Unlike the machine-learning comparison notebook, it fits its statistical models during execution.

Figures: `figure/Hockey_stick1.jpg`, `figure/Hockey_stick2.jpg`.

## Software Environment

Use Python 3.11 with the package versions in `requirements.txt`:

```bash
python -m pip install -r requirements.txt
```

CPU inference is sufficient for the saved LSTM model. For GPU execution, install the appropriate PyTorch 2.5.1 build for your platform using the [official PyTorch installation instructions](https://pytorch.org/get-started/previous-versions/).

## Usage

Open a notebook in `code/` and run the cells in order, using **`code/` as the kernel working directory** — the notebooks read `../data/`, `../models/`, and `../results/`, and save figures to `../figure/`.

## Notes and Limitations

- Results may differ slightly across software versions, package versions, and CPU/GPU environments. The recorded requirements reflect the local analysis environment, not the GPU training environment.
- KDCA-based classifications generated in this study are not official forecasts or operational warnings issued by KDCA.
- The `previous/` folder preserves the earlier notebooks, data, and model files as archived materials; they are not the revised analysis files described above.

## Data Sources

- **Korea Disease Control and Prevention Agency (KDCA):** HRI surveillance
- **Korea Meteorological Administration (KMA):** meteorological data, heatwave warnings, and SSP climate projections
- **Korean Statistical Information Service (KOSIS):** regional demographic and socioeconomic data

Source data remain subject to the applicable terms of their original providers.

## Citation

**Outbreak Detection of Heat-Related Illness Under Climate Changes: Development and Evaluation of a Machine Learning-Based Method**

Full publication details and the DOI will be added when available.
