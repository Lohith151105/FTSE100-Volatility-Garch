# Modelling and Forecasting FTSE 100 Volatility

## Research question

How well do GARCH-family models capture and forecast volatility in
FTSE 100 returns, and does allowing for asymmetric volatility improve
out-of-sample forecasts?

## Planned design

- Market: FTSE 100 price index.
- Frequency: daily.
- Planned sample: January 2000 to August 2026, subject to data availability.
- Returns: 100 times the first difference of log closing prices.
- Forecast target: one-trading-day-ahead conditional variance.
- Benchmark: EWMA.
- Models: GARCH(1,1), GJR-GARCH(1,1), and asymmetric EGARCH(1,1).
- Main innovation distribution: standardized Student-t.
- Initial estimation period: 2000–2017.
- Planned evaluation period: 2018–August 2026.
- Main estimation scheme: expanding window.
- Primary forecast loss: QLIKE.
- Secondary losses: MSE, RMSE, and MAE.

## Evaluation considerations

Squared returns are the primary observable variance proxy, using a
negligible-conditional-mean approximation. A common past-data-only
mean adjustment will be considered as a sensitivity check.

A range-based proxy remains provisional, subject to OHLC data quality
and acknowledgement that session ranges omit overnight movements.

## Status

Research design and initial literature foundation completed.
Project setup underway. No empirical results yet.

## Structure

- data/raw/: original downloaded observations.
- data/processed/: cleaned data and constructed variables.
- notebooks/: analysis and explanations.
- outputs/figures/: exported charts.
- outputs/tables/: exported results.
- report/: final research report.

## Environment

Create and activate a Python virtual environment, then install:

    python -m pip install -r requirements.txt

Select the Python (FTSE Volatility) notebook kernel.
