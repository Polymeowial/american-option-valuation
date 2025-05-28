# Valuing American Option using LSM method

## Overview

Valuing American puts using Least-squares Monte Carlo (LSM) of Longstaff and Schwartz (2001). Data: Microsoft daily closing price from May 14th 2024 to May 14th 2025


Link to their paper: [Longstaff and Schwartz (2001)](https://www.bing.com/ck/a?!&&p=1d4050da31f5a12d1c027a4b687fdee45f2918c6b2ef22d6747ac59175fa0a21JmltdHM9MTc0ODA0NDgwMA&ptn=3&ver=2&hsh=4&fclid=1822aef0-1d75-6a9b-2f67-bdb71c136b36&psq=longstaff+schwartz+LSM+2001+financial+review&u=a1aHR0cHM6Ly9wZW9wbGUubWF0aC5ldGh6LmNoLyU3RWhqZnVycmVyL3RlYWNoaW5nL0xvbmdzdGFmZlNjaHdhcnR6QW1lcmljYW5PcHRpb25zTGVhc3RTcXVhcmVNb250ZUNhcmxvLnBkZg&ntb=1)

## Requirements

All Python package dependencies are listed in `requirements.txt`. To install them, run:

```bash
pip install -r requirements.txt
```

## Methodology

Assume stock price $S_t$ to follow the Geometric Brownian Motion (GBM): <br>
$dS = \mu S dt + \sigma S dW$ <br>
where $W$ is a Wiener process.

American put value is defined as <br>
$V_t = \max_{\tau \in \mathcal{\tau}} E^\mathbb{Q} \[ e^{r (\tau - t)} (K - S_t) \ | \mathcal{F}_t]$ <br>

where $\tau$ is a exercising strategy among the set of all possibles exercising strategy $\tau$.






## Notebook sections
Part A
Part B
, etc.
