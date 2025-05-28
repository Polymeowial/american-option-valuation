# Valuing American Option using LSM method

## Overview

This project values American put options using the Least-Squares Monte Carlo (LSM) approach of Longstaff and Schwartz (2001). <br>
Data used in this project is the daily closing price of Microsoft (NASDAQ:MSFT) from May 14th 2024 to May 14th 2025.

The code is contained in LSM.ipynb and data is in MFST.csv

For more details about this method, see [Longstaff and Schwartz (2001)](https://www.bing.com/ck/a?!&&p=1d4050da31f5a12d1c027a4b687fdee45f2918c6b2ef22d6747ac59175fa0a21JmltdHM9MTc0ODA0NDgwMA&ptn=3&ver=2&hsh=4&fclid=1822aef0-1d75-6a9b-2f67-bdb71c136b36&psq=longstaff+schwartz+LSM+2001+financial+review&u=a1aHR0cHM6Ly9wZW9wbGUubWF0aC5ldGh6LmNoLyU3RWhqZnVycmVyL3RlYWNoaW5nL0xvbmdzdGFmZlNjaHdhcnR6QW1lcmljYW5PcHRpb25zTGVhc3RTcXVhcmVNb250ZUNhcmxvLnBkZg&ntb=1)

## Requirements

All Python package dependencies are listed in `requirements.txt`. To install them, run:

```bash
pip install -r requirements.txt
```

## LSM Algorthim 

Using Monte Carlo simulation, we generate ``nTraj`` Geometric Brownian Motion (GBM) price paths. For each price path, using LSM algorthim, we will obtain a value for an American put. The final valuation for an American put is the average of these values.

LSM determines the value of American put at time 0 using a backward induction process, starting from time $t_\text{NStep}$ (maturity) to the time 0. <br>
- At maturity $t_\text{NStep}$, ``RealizedPayOff`` = $\max(Strike - S_\text{NStep}, 0)$ (Immediate exercise pay-off) <br> <br>
- At time $t_k < t_\text{NStep}$, we will determine whether this is the optimal time to exercise, if it is, we adjust the ``RealizedPayOff``, if not, we simply discount the ``RealizedPayOff`` from previuous time step ($t_{k+1}$). <br>
Specifically, we will exercise if

$$
\text{Exercise} =
\begin{cases}
\text{Yes, if the put is in-the-money and Immediate execise pay-off > Continuation value},\\
\text{No, otherwise.}
\end{cases}
$$

where __Immediate exercise pay-off__ = $\max(Strike - S_{t_k} ,0)$ <br> <br>
and __Continuation value__ is estimated using a linear regression on stock price $S_{t_k}$ wrt the discount of ``RealizedPayOff`` from previuous time ($t_{k+1}$), specifically, it is the fitted value of this regression. The functional form of the regression used in this project is a 3rd-degree polynomial.

Continue this process backwardly till time 0, we obtain the ``RealizedPayOff`` values at time 0, average these we will get the valuation for American put.


## Notebook Summary
Below is the summary for each part in LSM.ipynb

- Part A: Importing functions and modules for the project.
- Part B: Visualizing MFST daily closing price
- Part C: The code for LSM method <br>
  The LSM_Put() function implements the core LSM algorithm, while the other functions serve as helper functions.
- Part D: Estimating 2 parameters, volatility ``sigma`` and risk-free rate ``r``, for the GBM model of stock price. <br>
``sigma`` is estimated using the sample standard deviation on log returns. <br>
``r`` is estimated using the 4-week T-bills obtained from FRED.
- Part E: Comparing the observed market value with alues obtained from LSM. <br>
In-the-money puts is collected from Yahoo finance on May 14th 2025. These observed values is then compared to the values obtained from LSM.
- Part F: Exploring the difference between European put and American put (obtained from LSM method) at different strike prices for a short term maturity (15 days) and a longer term maturity (1 year)
