# Outbreak Detection of Heat-Related Illness Under Climate Changes: Development and Evaluation of a Machine Learning-Based Method

## Overview

This repository provides data, trained models, prediction results, and analysis code associated with our study of heat-related illness (HRI) in South Korea.

We analyzed historical HRI cases during 2016–2023 and projected future cases during 2024–2070 under four climate scenarios: SSP1-2.6, SSP2-4.5, SSP3-7.0, and SSP5-8.5.

The shared materials support inspection of model outputs and reproduction of selected analyses and figures presented in the manuscript.

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
    ├── Model_comparison.jpg
    ├── warning_comparison_national.jpg
    ├── warning_comparison_regional.jpg
    ├── Hockey_stick1.jpg
    └── Hockey_stick2.jpg
```

The tree lists the currently supplied files. Rerunning `Model_comparison.ipynb` saves its regional figure as `Model_comparison_regional.jpg`; the supplied regional image is named `Model_comparison.jpg`.

The `previous/` folder preserves the earlier notebooks, data, and model files in their original relative directory structure. These are archived materials, not the revised analysis files described below.

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

$WBGT_{weight,t} = WBGT_{t} + \alpha × WBGT_{t−1} + \beta × WBGT_{t−2}$

Candidate values for $\alpha$ and $\beta$ ranged from 0.1 to 1.0 in increments of 0.1.

## Data

### Observed HRI Data

| File | Contents |
|---|---|
| [Final_heat_region.xlsx](data/Final_heat_region.xlsx) | Regional HRI case counts, meteorological variables, and regional characteristics used by the analysis notebooks. |

Historical observations cover each year’s HRI surveillance period. The input workbook contains supporting columns beyond the 18 predictors selected by the model-comparison notebook.

### SSP Scenario Inputs

Future input data cover 2024–2070. Input and result files are paired as follows:

| Scenario | Input data | Predicted HRI cases |
|---|---|---|
| SSP1-2.6 | [Final_SSP126.csv](data/Final_SSP126.csv) | [SSP126_result.csv](results/SSP126_result.csv) |
| SSP2-4.5 | [Final_SSP245.csv](data/Final_SSP245.csv) | [SSP245_result.csv](results/SSP245_result.csv) |
| SSP3-7.0 | [Final_SSP370.csv](data/Final_SSP370.csv) | [SSP370_result.csv](results/SSP370_result.csv) |
| SSP5-8.5 | [Final_SSP585.csv](data/Final_SSP585.csv) | [SSP585_result.csv](results/SSP585_result.csv) |

These inputs include meteorological variables, derived heat-exposure indicators, and regional characteristics.

The inputs use one row per region and date. The consecutive-exposure variable is named `con` in the SSP files and `cons` in the historical model feature list; align these names before passing future inputs to a model.

Regional demographic projections were used through 2052. Values for 2053–2070 were extrapolated using region-specific linear trends estimated from the 2043–2052 projections. The baseline applied the full estimated trends.

## Results

### Historical Model Predictions

The workbook [Model_result.xlsx](results/Model_result.xlsx) contains four sheets: `LR`, `RF`, `XGB`, and `LSTM`. Each sheet includes observed and predicted HRI cases, with training, validation, and test periods identified separately.

| Field | Description |
|---|---|
| `region` | Study region |
| `date` | Date corresponding to the observation and prediction |
| `observed` | Observed number of HRI cases |
| `predicted` | Model-predicted number of HRI cases |
| `period` | `train`, `validation`, or `test` |

Predictions are continuous-valued outputs and are not rounded to integer counts. Prediction dates may differ from the full surveillance period because consecutive input observations are required.

### Future HRI Projections

Future results contain LSTM-based HRI projections for each SSP scenario during 2024–2070.

Each `SSP*_result.csv` uses a wide format: `date`, one prediction column per region, `year`, `Korea` (the summed prediction across study regions), and `date2` (month-day). These are previously generated outputs, distinct from the input files in `data/`.

### Parameter Selection

| File | Contents |
|---|---|
| [ML_parameter_selection.csv](results/ML_parameter_selection.csv) | LR, RF, and XGBoost results by α/β combination, including validation/test MSE, RF/XGBoost hyperparameters, and run settings. |
| [LSTM_parameter_selection.csv](results/LSTM_parameter_selection.csv) | LSTM hyperparameters and repeated-seed validation summaries by α/β combination, including mean, median, standard deviation, minimum, maximum, and ranking. |

`mean_valid_mse` in the LSTM summary is an average across seed runs, not the validation MSE of a single saved checkpoint. Test metrics in the ML table are reported separately from the validation metrics used for parameter selection.

## Trained Models

The `models/` directory contains the selected trained models:

| File | Model | Loading method |
|---|---|---|
| `LR_model.pkl` | Linear regression | `joblib.load` |
| `RF_model.pkl` | Random forest | `joblib.load` |
| `XGB_model.json` | XGBoost | `XGBRegressor.load_model` |
| `LSTM_model.pth` | LSTM | `torch.load`, followed by `load_state_dict` |

The LSTM checkpoint includes weights, hyperparameters, feature order, sequence length, training/validation years, and feature-scaling values. The model-comparison notebook reconstructs LR/RF/XGBoost scaling from the historical training data. Only load serialized models from trusted sources.

When using these models:

- Preserve the input feature names and order.
- Apply the preprocessing and normalization used during training.
- Use the model-specific α and β values when calculating weighted WBGT.
- Preserve the sequence length required by the model.
- Set the LSTM model to evaluation mode before prediction.

Small numerical differences may occur across software versions and CPU/GPU environments.

## Analysis Code

### Model Comparison

[Model_comparison.ipynb](code/Model_comparison.ipynb) loads `data/Final_heat_region.xlsx` and the four saved models. It calculates training, validation, and test metrics, aggregates regional predictions to national totals, and plots national and regional observed–predicted comparisons. It does not retrain the models.

Figures are saved to `figure/Model_comparison_national.jpg` and `figure/Model_comparison_regional.jpg`.

### Warning Comparison

[Warning_comparison.ipynb](code/Warning_comparison.ipynb) uses `data/Final_heat_region.xlsx` and the `LSTM` sheet of `results/Model_result.xlsx` to compare warning periods during 2022–2023. It computes KMA-related warning indicators from WBGT-derived apparent temperature and applies KDCA-based HRI risk levels to observed and predicted case counts. The KMA-related indicators are calculated in the notebook rather than loaded from an official warning-issuance log.

Figures are saved to `figure/warning_comparison_national.jpg` and `figure/warning_comparison_regional.jpg`.

KDCA-based classifications generated in this study should not be interpreted as official forecasts or operational warnings issued by KDCA.

### Hockey-Stick Analysis

[Hockey_stick.ipynb](code/Hockey_stick.ipynb) reads `data/Final_heat_region.xlsx`, fits regional WBGT–HRI relationships using a Poisson hockey-stick model, and calculates approximate confidence intervals using parametric draws. Unlike the machine-learning comparison notebook, it fits its statistical models during execution.

Regional plots are saved to `figure/Hockey_stick1.jpg` and `figure/Hockey_stick2.jpg`.

The current `code/` directory contains these three notebooks. SSP input data and projection results are supplied, but a dedicated SSP preprocessing, forecasting, or visualization notebook is not included in this version.

## Software Environment

Use Python 3.11 with the package versions listed in `requirements.txt`. These versions were recorded from the local analysis environment used to prepare this repository. They are not a complete record of the GPU training environment or a guarantee of identical results on every platform.

Install the dependencies in a separate environment from the repository root:

```bash
python -m venv .venv
```

Activate the environment:

- Windows PowerShell: `.\.venv\Scripts\Activate.ps1`
- Linux/macOS: `source .venv/bin/activate`

Then install the dependencies and register the notebook kernel:

```bash
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
python -m ipykernel install --user --name heat-risk-warning --display-name "Python (Heat-risk-warning)"
```

Select **Python (Heat-risk-warning)** in your existing Jupyter or VS Code installation. The requirements include notebook kernel support, but not a Jupyter user interface.

CPU inference is sufficient for using the saved LSTM model. If GPU execution is needed, install the appropriate PyTorch 2.5.1 build for your platform using the [official PyTorch installation instructions](https://pytorch.org/get-started/previous-versions/). Loading serialized scikit-learn models also requires compatibility with the environment in which they were saved.

## Usage

1. Install the dependencies and select the notebook kernel as described above.
2. Retain the repository directory structure and ensure that `figure/` exists.
3. Open a notebook in `code/` and use **`code/` as the kernel working directory**. The notebooks read `../data/`, `../models/`, and `../results/`, and save figures to `../figure/`.
4. Restart the kernel before switching notebooks and execute cells in order, subject to the warning-notebook note below.
5. Inspect the generated figures in `figure/`. Existing figures with matching output names will be overwritten.


## Data Sources

The study used information from:

- **Korea Disease Control and Prevention Agency (KDCA):** HRI surveillance
- **Korea Meteorological Administration (KMA):** meteorological data, heatwave warnings, and SSP climate projections
- **Korean Statistical Information Service (KOSIS):** regional demographic and socioeconomic data

Source data remain subject to the applicable terms of their original providers.


## Citation

**Outbreak Detection of Heat-Related Illness Under Climate Changes: Development and Evaluation of a Machine Learning-Based Method**

Full publication details and the DOI will be added when available.
