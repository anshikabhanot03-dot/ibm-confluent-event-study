# IBM–Confluent M&A Event Study

## Overview

This project implements an event study of IBM’s announced acquisition of Confluent Inc. (CFLT) on 8 December 2025, using daily equity returns and a market-model framework. The goal is to assess whether financial markets reacted significantly to the acquisition announcement for both the acquirer (IBM) and the target (Confluent).

The analysis is conducted in Python and demonstrates an end-to-end workflow: data acquisition, return construction, model estimation, abnormal return calculation, hypothesis testing, diagnostics, and robustness checks.

---

## Research Question

- Did IBM and Confluent experience statistically significant abnormal returns around the acquisition announcement date?  
- How does the pattern of abnormal returns compare between the acquirer and the target, and how does it align with typical M&A announcement effects?

---

## Data and Methodology

### Data

- **Tickers:** IBM (acquirer), CFLT (target), S&P 500 (^GSPC) as the primary market benchmark.  
- **Source:** Yahoo Finance, retrieved programmatically using `yfinance`.  
- **Sample period:** 1 June 2025 – 28 December 2025, providing sufficient pre-event data for estimation and post-event data for the event window.  

### Event and Windows

- **Event date:** 8 December 2025 (IBM’s acquisition announcement of Confluent); the nearest trading day in the return series is used as the event date.  
- **Estimation window:** −120 to −10 trading days before the event.  
- **Event window:** −10 to +10 trading days around the event, with robustness checks using narrower windows.  

### Model

A standard **market model** is estimated via OLS separately for IBM and CFLT:

\[
R_{i,t} = \alpha_i + \beta_i R_{m,t} + \varepsilon_t
\]

where \(R_{i,t}\) is the stock’s daily return and \(R_{m,t}\) is the market return (S&P 500). Abnormal returns (AR) are defined as actual minus model-implied returns; cumulative abnormal returns (CAR) are the cumulative sum of AR over the event window.

To test robustness, the analysis can be repeated using a tech ETF benchmark (e.g., QQQ) as an alternative market proxy.

---

## Key Results

### IBM and Confluent (S&P 500 benchmark)

| Stock | AR t-test | AR p-value | CAR t-test | CAR p-value | Interpretation |
|-------|-----------|------------|------------|-------------|----------------|
| IBM   | -0.18     | 0.86       | 5.10       | <0.0001     | Statistically significant positive CAR; daily AR not significant |
| CFLT  | 0.96      | 0.35       | 3.90       | 0.0009      | Statistically significant positive CAR for target; daily AR not significant |

- **Daily ARs:** Neither IBM nor CFLT exhibit statistically significant average daily abnormal returns over the event window.  
- **Cumulative CAR:** Both IBM and CFLT show statistically significant positive cumulative abnormal returns, with stronger gains for the target (Confluent).  

### Interpretation

- **IBM (Acquirer):** IBM’s stock shows a positive cumulative reaction over the event window, even though individual daily abnormal returns are small and not statistically significant.  
- **Confluent (Target):** Confluent shareholders experience positive abnormal performance that builds over several days rather than on a single announcement day.  

This pattern—modest but positive acquirer reaction and stronger positive target reaction—is broadly consistent with empirical evidence from M&A announcement studies.

---

## Diagnostics and Limitations

### Diagnostics

- The market model delivers modest R² values (around 0.10–0.12), which is typical for daily equity returns.  
- Residual diagnostics show pronounced non-normality and fat tails, especially for CFLT (large Jarque–Bera statistics and high kurtosis), indicating deviations from classical OLS assumptions.  

### Limitations

- **Parametric tests:** One-sample t-tests on AR and CAR treat residuals as approximately normal and independent; given the observed non-normality and potential serial correlation, p-values should be interpreted as approximate rather than exact.  
- **Event-study simplifications:** The CAR tests do not explicitly adjust for overlapping windows or cross-sectional dependencies; more advanced methods (e.g., standardized residual tests, bootstrapping, or non-parametric tests) could refine inference in a research setting.  
- **Confounding news and leakage:** Other news around the event window or information leakage prior to the official announcement could influence returns and are not explicitly modelled.  
