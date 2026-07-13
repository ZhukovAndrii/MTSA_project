# Inflation Forecasting: VAR vs. AR

This project compares the forecasting performance of a multivariate Vector Autoregression (VAR) model with a univariate Autoregression (AR) model for inflation forecasting.

The main goal is to test whether including additional macroeconomic variables, specifically unemployment and a short-term interest rate, improves inflation forecasts compared with using only past inflation values.

The analysis is carried out on monthly data for:
- the Euro area, using ECB data;
- the United States, using FRED data.

The project includes stationarity and cointegration testing, lag selection, AR and VAR estimation, model diagnostics, Granger causality testing, and out-of-sample forecast evaluation using RMSE, MAE, and the Diebold–Mariano test.

The main finding is that the VAR model does not outperform the univariate AR model. In the Euro area, the seasonal AR(12) model performs best at short horizons, while in the U.S. the AR model clearly outperforms the VAR at longer forecast horizons. :contentReference[oaicite:0]{index=0}

## Repository Contents

- `code_EU.ipynb` — analysis for the Euro area
- `code_US.ipynb` — analysis for the United States

## Authors

- Andrii Zhukov
- Daniil Afanasiev
