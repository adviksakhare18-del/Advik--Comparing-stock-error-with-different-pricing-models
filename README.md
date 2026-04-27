# Comparing Stock Error with Different Stock Pricing Models

**Author:** Advik Sakhare  
**Date:** 17/04/2026  
**Companion paper:** [Testing Varying Shock Functions](INSERT_LINK_HERE)

---

## Overview

This repository contains an MS Excel investigation and accompanying report comparing the short-term predictive accuracy of three stochastic stock pricing models:

- **Geometric Brownian Motion (GBM)**
- **Ornstein–Uhlenbeck Mean Reversion (OUMR)**
- **Cox–Ingersoll–Ross (CIR)**

Each model is used to predict the stock price 11 days ahead using 2 years of historical data (1/1/24–1/1/26), pulled directly from Microsoft's database via Excel's `STOCKHISTORY()` function. The predictions are then compared against actual historical prices, and the percentage error is measured across 500 Monte Carlo simulation runs per model, per stock. The models are assessed on their mean percentage error and standard deviation of error.

---

## Repository contents

```
/
├── stock_models.xlsx       # The Excel model and Monte Carlo simulation
└── report.pdf              # The full written report
```

---

## Models

### Geometric Brownian Motion (GBM)

The simplest of the three models. Stock price evolves according to:

$$dS_t = \mu S_t \, dt + \sigma S_t \, dW_t$$

Discretised as:

$$S_{t+1} = S_t \exp\!\left[\left(\mu - \frac{\sigma^2}{2}\right)\Delta t + \sigma\sqrt{\Delta t}\, Z\right]$$

where $Z \sim N(0,1)$, evaluated using `NORM.S.INV()`.

---

### Ornstein–Uhlenbeck Mean Reversion (OUMR)

A mean-reverting model well suited to commodity prices. Stock price evolves according to:

$$dS_t = \theta(\bar{S} - S_t)\, dt + \sigma \, dW_t$$

Discretised (Euler–Maruyama) as:

$$S_{t+1} = S_t + \theta(\bar{S} - S_t)\,\Delta t + \sigma\sqrt{\Delta t}\, Z$$

where $\theta > 0$ controls the strength of mean reversion and $\bar{S}$ is the running average price.

---

### Cox–Ingersoll–Ross (CIR)

A close cousin of OUMR where volatility scales with the square root of the current price, preventing the stock from going negative (given Feller's condition $2\theta\bar{S} > \sigma^2$ holds). Stock price evolves according to:

$$dS_t = \theta(\bar{S} - S_t)\, dt + \sigma\sqrt{S_t}\, dW_t$$

Discretised as:

$$S_{t+1} = S_t + \theta(\bar{S} - S_t)\,\Delta t + \sigma\sqrt{\Delta t}\, Z$$

where $Z = \max(S_t, 0)$ to enforce non-negativity.

---

## Method

1. 2 years of historical daily close prices are pulled via `STOCKHISTORY()`.
2. Standard returns and log returns are computed.
3. Model parameters ($\mu$, $\sigma$, $\theta$, $\bar{S}$) are estimated from the historical data.
4. Each model predicts the stock price for the next 11 trading days.
5. The absolute percentage error against actual prices is computed:

$$\text{Abs \% error} = \frac{|\text{Predicted price} - \text{Historical price}|}{\text{Historical price}} \times 100\%$$

6. Steps 4–5 are repeated 500 times via Monte Carlo simulation.
7. The mean and standard deviation of error across the 500 runs are recorded and graphed.

This is done across two sectors: **blue-chip tech stocks** (MSFT, AAPL, NVDA, AMZN, META) and **commodity-based stocks** (RIO, S32, BHP, NEM, CTVA).

---

## Key findings

- **GBM** performed the worst consistently across both sectors, as expected given its simplistic assumptions of constant volatility and normally distributed returns.
- **OUMR** and **CIR** produced approximately the same mean percentage error. CIR exhibited marginally higher standard deviation, making OUMR slightly more consistent on average.
- **OUMR** showed a particular advantage for commodity-based stocks, where mean-reverting behaviour is a realistic assumption.
- **CIR** performed strongest for blue-chip tech stocks, where its price-proportional volatility and non-negativity constraint better reflect real market dynamics.

---

## How to use the Excel model

> The `STOCKHISTORY()` function must be available and working in your version of Excel. If it is not, the data will not load and the model, graphs, and simulations will not work.

All required inputs are **yellow cells with red text**.

1. Enter the ticker of the stock you want to analyse in cell **A1**.
2. If the graphs show all zeros after changing the ticker, this is normal — go to **Formulas → Calculation → Calculate Sheet**.
3. The 500 Monte Carlo runs will populate and the summary graphs will update.
4. If the mean error or standard deviation for any model appears extremely high, recalculate the sheet. This can be a characteristic of the model or may indicate the ticker is not supported by `STOCKHISTORY()`.

---

## Assumptions and limitations

- Returns are assumed to be normally distributed for consistency with standard quantitative backtesting practice, even though this is not strictly correct (see the companion Parabola paper linked above for a detailed discussion).
- Only 2 years of historical data is used, which limits the generalisability of the results and introduces a risk of overfitting to a specific market regime.
- Penny stocks were excluded from the investigation as their historical data is too inconsistent and in many cases does not stretch back the full 2 years required by the model.
- Overfitting checks are not currently implemented. The naïve assumption is adopted that lower mean error and standard deviation imply a better model.

---

## Future work

This paper is the second in a series. A follow-up paper will extend the analysis to:

- GARCH(1,1) and its variants
- Variance Gamma (VG)
- Heston Stochastic Volatility (HSV)


By: Advik Sakhare
B.Actuarial Studies/B. Advanced Math (Hons). @ UNSW
