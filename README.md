# Inflation Forecasting: VAR vs. AR

**Does adding unemployment and interest-rate information improve inflation forecasts?**

This project compares a univariate autoregressive model (**AR**) with a multivariate vector autoregression (**VAR**) for forecasting inflation in the **Euro area** and the **United States**. The goal is not only to compare forecast errors, but to build and validate a complete time-series forecasting pipeline: data acquisition, stationarity and cointegration testing, lag selection, model diagnostics, expanding-window backtesting, and statistical forecast comparison.

**Course:** Multivariate Time Series Analysis, University of Vienna  
**Tools:** Python, pandas, NumPy, statsmodels, SciPy, Matplotlib

---

## Research question

A VAR can use information from several macroeconomic variables, but this flexibility comes with many more parameters to estimate.

We ask:

> **Does a VAR using inflation, unemployment, and a short-term interest rate forecast inflation more accurately than an AR model using only inflation's own history?**

The comparison is repeated for two economies to test whether the conclusion is robust across different monetary environments.

---

## What this project demonstrates

- Building an **end-to-end forecasting workflow** using real macroeconomic data from official APIs
- Working with **non-stationary time series** using ADF tests and first differences
- Testing for **cointegration** with the Johansen trace test
- Selecting lag orders using **AIC, BIC, HQIC, and FPE**
- Estimating and comparing **AR and VAR models**
- Checking **model stability and residual autocorrelation**
- Testing **Granger causality**
- Designing a **leakage-free expanding-window backtest**
- Evaluating forecasts with **RMSE, MAE, and Diebold–Mariano tests**
- Comparing results across the **Euro area and the United States**

---

## Data

The project uses three monthly macroeconomic series for each economy.

| Economy | Inflation | Unemployment | Interest rate | Source |
|---|---|---|---|---|
| Euro area | HICP annual inflation | Harmonised unemployment rate | 3-month EURIBOR | ECB Data Portal |
| United States | CPI-U year-on-year inflation | Civilian unemployment rate | Effective federal funds rate | FRED |

The models are specified using data through **December 2014**. Forecast performance is then evaluated from **January 2015 onward** using an expanding window: at every forecast origin, each model is re-estimated using only information available up to that date.

The notebooks download the data directly from the ECB and FRED APIs.

---

## Methodology

The same general pipeline is applied to both economies:

1. **Stationarity testing**  
   Augmented Dickey–Fuller tests are used to examine unit roots. The models are estimated on first differences when levels are treated as non-stationary.

2. **Cointegration testing**  
   The Johansen trace test checks whether a long-run equilibrium relationship should be preserved through a VECM rather than removed by differencing.

3. **Lag-order selection**  
   Candidate AR and VAR lag lengths are compared using AIC, BIC, HQIC, and FPE, while accounting for the much larger parameter cost of a multivariate VAR.

4. **Model estimation and diagnostics**  
   AR and VAR models are fitted and checked using residual autocorrelation tests, VAR stability diagnostics, and Granger-causality tests.

5. **Out-of-sample forecasting**  
   Forecasts are produced with an **expanding window** and no look-ahead. Models are compared at horizons of **1, 3, 6, and 12 months**.

6. **Forecast evaluation**  
   Performance is measured using **RMSE** and **MAE**, with a **no-change forecast** as a simple benchmark. One-step forecast accuracy is also compared using the **Diebold–Mariano test with a Newey–West variance estimate**.

---

## Results

### Euro area

The seasonal **AR(12)** gives the lowest RMSE at short and medium horizons, while the VAR does not provide a statistically significant improvement.

| Horizon | VAR(2) | AR(12) | No-change |
|---:|---:|---:|---:|
| 1 month | 0.399 | **0.369** | 0.425 |
| 3 months | 0.842 | **0.820** | 0.932 |
| 6 months | 1.479 | **1.445** | 1.595 |
| 12 months | **2.728** | 2.876 | **2.728** |

Additional evidence:

- Unemployment and the interest rate do **not** significantly Granger-cause inflation in the fitted VAR (`p ≈ 0.456`).
- VAR(2) and the lag-matched AR(2) produce very similar forecasts.
- The one-step Diebold–Mariano comparison between VAR(2) and AR(12) does not reject equal predictive accuracy (`p ≈ 0.163`).
- AR(12) captures the yearly pattern in monthly inflation with relatively few parameters, while adding long seasonal lag structures to a three-variable VAR is much more expensive.

### United States

The multivariate models perform substantially worse at longer horizons. The **AR(24)** performs best among the AR/VAR models at short horizons, while the simple no-change benchmark becomes competitive at longer horizons.

| Horizon | VAR(2) | AR(24) | No-change |
|---:|---:|---:|---:|
| 1 month | 0.402 | **0.302** | 0.385 |
| 3 months | 1.215 | **0.800** | 0.881 |
| 6 months | 3.093 | 1.392 | **1.375** |
| 12 months | 14.467 | 2.502 | **2.270** |

A richer VAR(12) does not solve the problem: its 12-month RMSE rises to approximately **26.9**, illustrating the cost of estimating many multivariate lag coefficients from a limited sample.

---

## Main takeaway

> **More variables do not automatically produce better forecasts.**

For this problem, the additional unemployment and interest-rate information in a VAR does not compensate for the model's higher parameter cost. A carefully specified univariate AR remains a strong benchmark for inflation forecasting in both economies.

The project also shows why **out-of-sample evaluation matters**: in-sample statistical relationships are not enough if they fail to improve actual forecasts.

---

## Limitations

- The analysis uses **final-vintage/revised data**, not the real-time data releases available to forecasters at each historical date.
- Forecast evaluation is based on one expanding-window period.
- Formal Diebold–Mariano inference is focused on the **one-step horizon**; longer-horizon comparisons are descriptive.
- The 2020–2023 period contains unusually large macroeconomic shocks that challenge constant-parameter linear models.
- Euro-area cointegration evidence is borderline, so a **VECM** is a natural extension.
- The models are linear and have fixed coefficients; regime-switching or time-varying models could capture structural changes differently.

---

## Repository contents

- [`code_EU.ipynb`](code_EU.ipynb) — complete Euro-area analysis
- [`code_US.ipynb`](code_US.ipynb) — U.S. replication using the same forecasting framework

Each notebook contains the data-loading code, statistical tests, model estimation, diagnostics, backtesting, result tables, and visualisations required to reproduce the analysis.

---

## Running the project

Install the required packages:

```bash
pip install numpy pandas scipy matplotlib statsmodels requests jupyter
```

Then start Jupyter:

```bash
jupyter lab
```

Run `code_EU.ipynb` and `code_US.ipynb` from top to bottom. The notebooks download the macroeconomic series directly from the ECB and FRED.

---

## Possible extensions

Several extensions would make the comparison more realistic or more flexible:

- estimate a **VECM** for the Euro-area system;
- use **Clark–West** tests for nested forecast comparisons;
- extend forecast-accuracy inference to multiple horizons;
- evaluate models using **real-time data vintages**;
- test **time-varying parameter**, **state-space**, or **regime-switching** models;
- compare against nonlinear or machine-learning forecasting approaches while retaining simple AR/no-change benchmarks.

---

## Authors

**Andrii Zhukov**  
**Daniil Afanasiev**

University of Vienna
