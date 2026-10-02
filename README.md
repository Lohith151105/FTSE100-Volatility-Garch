# FTSE 100 Volatility Modelling and Forecasting

An empirical comparison of GARCH, GJR-GARCH and EGARCH models for forecasting daily FTSE 100 variance, using EWMA as a benchmark.

The project combines trading-calendar validation, volatility diagnostics and out-of-sample forecasting in Python. It examines whether allowing positive and negative return shocks to affect volatility differently improves forecast accuracy.

## Key Findings

- **EGARCH-t achieved the lowest average QLIKE and MSE among the baseline models**, reducing MSE by **6.33% relative to EWMA** across 2,188 out-of-sample trading sessions.
- Its QLIKE improvement over EWMA was statistically significant using HAC inference with 10 lags (**p = 0.0009**).
- Both asymmetric models improved on symmetric GARCH under QLIKE. However, EGARCH-t’s advantage over GJR-GARCH-t was **not statistically significant** (**p = 0.2912**).
- EGARCH’s MSE improvement over EWMA remained positive when excluding 2020, using normal innovations, adopting a rolling estimation window and evaluating against a Parkinson high–low variance proxy.

## Data

Daily FTSE 100 prices cover the modelling period from 2000 to August 2026.

- **Primary source:** Yahoo Finance.
- **Validation:** London Stock Exchange trading-calendar alignment, duplicate-date checks and missing-price investigation.
- **Corrections:** Historical OHLC prices from Investing.com were used to repair 22 December 2020 and insert the missing session on 28 May 2012.
- **Returns:** Daily log returns, calculated as `100 × log(Close_t / Close_t−1)`.
- **Initial training sample:** 4,548 observations, 2000–2017.
- **Out-of-sample evaluation:** 2,188 observations, 2 January 2018–28 August 2026.

Corrections are documented separately from the original source data.

## Methodology

### Models

The baseline specifications use a constant conditional mean and standardised Student’s t innovations:

- **GARCH(1,1):** symmetric response to return shocks.
- **GJR-GARCH(1,1,1):** additional response to negative shocks.
- **EGARCH(1,1,1):** asymmetric dynamics in log conditional variance.
- **EWMA:** zero-mean benchmark with a fixed decay factor of 0.94.

### Forecasting and evaluation

The GARCH-family models are re-estimated each trading day using an expanding window. Each one-day-ahead forecast uses only returns observed before its target date.

Forecasts are evaluated against squared daily returns using:

- QLIKE loss;
- mean squared error and root mean squared error;
- pairwise QLIKE loss comparisons with HAC standard errors;
- cumulative QLIKE gains relative to EWMA.

Training diagnostics include return and squared-return autocorrelations, Ljung–Box tests, ARCH–LM tests and standardised residual checks.

## Out-of-Sample Results

Lower QLIKE and MSE indicate better performance. MSE reductions are measured relative to EWMA.

| Model | Mean QLIKE | MSE | MSE reduction vs EWMA |
|---|---:|---:|---:|
| **EGARCH-t** | **0.575760** | **13.916075** | **6.33%** |
| GJR-GARCH-t | 0.584627 | 14.622742 | 1.57% |
| GARCH-t | 0.608455 | 14.519910 | 2.27% |
| EWMA | 0.656756 | 14.856511 | — |

GJR-GARCH-t ranks above GARCH-t under QLIKE, while GARCH-t ranks above GJR-GARCH-t under MSE.

![Cumulative QLIKE gains relative to EWMA](outputs/figures/oos_cumulative_qlike_gain.png)

Gains accumulate unevenly over time. Positive cumulative gains indicate lower total QLIKE loss than EWMA, rather than superior performance on every date.

## Robustness Checks

| EGARCH specification | Evaluation proxy | Observations | MSE reduction vs EWMA |
|---|---|---:|---:|
| Expanding window, Student’s t | Squared return | 2,188 | 6.33% |
| Excluding 2020 from evaluation | Squared return | 1,934 | 5.23% |
| Normal innovations | Squared return | 2,188 | 5.98% |
| Rolling window: 1,260 observations | Squared return | 2,188 | 4.14% |
| Parkinson proxy | High–low range | 2,188 | 30.59% |

The Parkinson measure evaluates a different target, so its improvement percentage is not directly comparable with the squared-return results. Excluding 2020 affects evaluation dates only; those observations remain available for subsequent model estimation.

## Repository Guide

| Location | Contents |
|---|---|
| `data/raw/` | Original source data |
| `data/corrections/` | Documented price corrections |
| `data/processed/` | Prepared modelling dataset |
| `notebooks/` | Data preparation, exploratory analysis, estimation and forecasting |
| `outputs/figures/` | Diagnostic and forecast-performance figures |
| `outputs/tables/` | Estimates, forecasts, diagnostics and robustness results |
| `report/report.md` | Full methodology, results and limitations |
| `requirements.txt` | Python dependencies |

Start with the [full report](report/report.md) for interpretation, or the [model estimation notebook](notebooks/03_model_estimation.ipynb) for the implementation.

## Running the Analysis

The project was developed using Python 3.12.4.

Clone the repository and create a virtual environment:

```bash
git clone https://github.com/Lohith151105/FTSE100-Volatility-Garch.git
cd FTSE100-Volatility-Garch
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
```

Open the project in VS Code and select `.venv` as the notebook kernel. Run the notebooks in numerical order, executing cells from top to bottom.

The modelling notebook includes repeated model estimation for the baseline forecasts and robustness checks, so a complete run takes longer than loading the saved outputs.

## Limitations

- Squared returns and the Parkinson measure are proxies for unobserved variance.
- Results concern one index, one historical evaluation period and one-day-ahead forecasts.
- The cleaned historical data do not reproduce real-time data vintages.
- Baseline pairwise QLIKE p-values are unadjusted for multiple comparisons; MSE improvements and robustness comparisons are descriptive.
- Forecast accuracy does not establish trading profitability or improved portfolio outcomes.