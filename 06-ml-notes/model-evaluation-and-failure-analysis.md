# Model Evaluation and Failure Analysis

The BTC project evaluates models with:

- MAE
- MSE
- RMSE
- R²

## Why one metric is not enough

A model can perform well on average while performing badly during extreme volatility.

This matters because extreme BTC movements are exactly the periods where volatility forecasting can become most useful.

## Failure pattern

The project showed that models can perform reasonably during normal volatility but underestimate extreme events.

One reason is the distribution of the target:

```
many normal observations
few extreme observations
```

With ordinary MSE training, the frequent normal regime can dominate the loss.

## Experiments

Possible ways to improve tail performance:

- log-transform the target
- weighted loss for high-volatility observations
- quantile regression
- walk-forward validation
- tail-specific evaluation

The important lesson is to investigate **where** a model fails, not only its average score.
