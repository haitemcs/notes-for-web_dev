# BTC Volatility Forecasting

Notes from my BTC volatility forecasting project.

Repository: [btc-volatility-forecast](https://github.com/haitemcs/btc-volatility-forecast)

## Goal

Predict the next-hour BTC volatility/range from historical market information.

A basic hourly range measure is:

```
R_t = (High_t - Low_t) / Low_t
```

The project uses historical market data and engineered features to estimate future volatility.

## Data

The project evolved from a simple market-data experiment into a more complete ML project using BTC/USDT hourly market data.

The later pipeline includes:

- OHLCV information
- order-flow related features
- funding-related features
- time-series target construction
- model training and evaluation

## Engineering direction

The project is not only about training a model. The goal is to connect:

```
Market Data
    ↓
Feature Engineering
    ↓
Time-Series Validation
    ↓
ML Model
    ↓
FastAPI
    ↓
PostgreSQL
    ↓
Docker
```
