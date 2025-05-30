# Valuing American Option using LSM method

## Overview
This project implements the Least-Squares Monte Carlo (LSM) method developed by Longstaff and Schwartz (2001) to value American put options. The approach leverages Monte Carlo simulation combined with regression techniques to estimate for the optimal exercise strategy.

We uses daily closing price data for Microsoft Corporation (NASDAQ: MSFT) from May 14th 2024 to May 14th 2025.
- The core implementation can be found in the LSM.ipynb notebook.
- The historical price data is stored in MSFT.csv.

For a detailed explanation of the LSM methodology, refer to the original paper by [Longstaff and Schwartz (2001)](https://www.bing.com/ck/a?!&&p=1d4050da31f5a12d1c027a4b687fdee45f2918c6b2ef22d6747ac59175fa0a21JmltdHM9MTc0ODA0NDgwMA&ptn=3&ver=2&hsh=4&fclid=1822aef0-1d75-6a9b-2f67-bdb71c136b36&psq=longstaff+schwartz+LSM+2001+financial+review&u=a1aHR0cHM6Ly9wZW9wbGUubWF0aC5ldGh6LmNoLyU3RWhqZnVycmVyL3RlYWNoaW5nL0xvbmdzdGFmZlNjaHdhcnR6QW1lcmljYW5PcHRpb25zTGVhc3RTcXVhcmVNb250ZUNhcmxvLnBkZg&ntb=1)

## Requirements

All Python package dependencies are listed in `requirements.txt`. To install them, run:

```bash
pip install -r requirements.txt
```

## LSM Algorthim 

Using Monte Carlo simulation, we generate ``nTraj`` price paths of stock under the Geometric Brownian Motion (GBM). For each simulated path, the LSM algorithm estimates the option’s value (``RealizedPayOff``), and the final valuation is computed as the average of these estimates.

LSM determines the value of American put at time 0 using a backward induction process, starting from time $t_\text{NStep}$ (maturity) to the time 0. <br>
- At maturity $t_\text{NStep}$, ``RealizedPayOff`` = $\max(Strike - S_\text{NStep}, 0)$ (Immediate exercise pay-off) <br> <br>
- At time $t_k < t_\text{NStep}$, the algorithm determines whether it is optimal to exercise the option at that time. If it is optimal, we update the ``RealizedPayOff``, if not, we simply discount the ``RealizedPayOff`` from previuous time step ($t_{k+1}$). <br>
Specifically, we will exercise if

$$
\text{Exercise} =
\begin{cases}
\text{Yes, if the put is in-the-money and Immediate execise pay-off $\max(Strike - S_{t_k} ,0)$ > Continuation value $F(t_k)$},\\
\text{No, otherwise.}
\end{cases}
$$

where the __Continuation value__, $F(t_k)$, is estimated using a linear regression on functions of stock price $S_{t_k}$ with respect to the discount ``RealizedPayOff`` from previous time ($t_{k+1}$), specifically, it is the fitted value of this regression.

This backward induction continues until time 0, where we obtain a set of ``RealizedPayOff`` values at time 0 for each path. The final estimate of the American put is the average of these payoffs.

## Covergence test
The functional form of the linear regression can be expressed as

$$
F(t_k) = \sum^{i = 1-> NBasis} a_i \cdot L_i(S_{t_k})
$$

where the functions $L_i(S)$ serve as the regression’s basis functions. 

To choose the optimal number of basis functions ($NBasis$), one can use the convergence diagnostic described in the original paper. The core idea involves estimating the exercising rule using one set of simulated paths (insampled paths) and then applying it to a different set of paths (outsampled paths). 

For this LSM method, we can fit the coefficients of the regression estimated using the insampled paths on the outsampled paths. A well-designed algorithm should produce outsampled values that are very close to the corresponding insampled values.


## Notebook Summary
Below is the summary for each part in the file LSM.ipynb

**Part A. Setups**: Import necessary functions and modules used throughout the project.

**Part B. Data**: Daily closing prices of MFST stock and its observed American put prices.
- The MSFT stock price is collected from May 14th 2024 to May 14th 2025. Data is collected on [MarketWatch](https://www.marketwatch.com/investing/stock/MSFT/download-data?startDate=5/14/2024&endDate=5/14/2025).
- The American put prices are collected on May 14th 2025. Only in-the-money puts are collected. Data is collected on Yahoo Finance.

**Part C. LSM**: The code for LSM method <br>
  The LSM_Put() function implements the core LSM algorithm and other functions in this part serve as helper functions.

**Part D. Checking model assumption**: Using visualization and some statistical tests to check whether stock price follow the assumption of GBM model.

**Part E. Estimating model parameters**: Estimate key parameters for the GBM model: <br>
- Volatility ``sigma`` is estimated using the sample standard deviation of log returns. <br>
- Risk-free rate ``r`` is estimated using the 4-week T-bills obtained from FRED (Federal Reserve Economic Data).

**Part F. Convergence test**: Covergence test <br>
Apply the converegence test to determine the optimal number of basis functions.

**Part G. Empirical results**: <br>
1. Compares the valuations obtained from LSM with the observed market prices for in-the-money American puts. <br>

2. Assesses sensitivity of the LSM valuation to changes in volatility ``sigma``.

3. Explore the pricing difference between European and American puts (valued using the LSM method) across a range of strike prices, for both short-term (15-day) and long-term (1-year) maturities.


