# Feature Engineering for BTC Volatility

## Why features matter

Raw OHLCV data gives the model basic market information, but volatility depends on relationships between price, volume, market activity, and recent history.

Useful feature groups in the project include:

- OHLCV-derived features
- rolling statistics
- price/range relationships
- order-flow information
- funding information
- lagged historical information

## Important rule

Features used to predict the future must only use information available before the prediction horizon.

For example:

```
features at time t → predict target at t + 1 hour
```

Using future information creates leakage and can make validation results look much better than real-world performance.
