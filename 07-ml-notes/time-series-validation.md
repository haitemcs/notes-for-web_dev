# Time-Series Validation

Random train/test splitting is dangerous for financial time series because it can mix future observations into the training set.

The safer mental model is:

```
PAST --------------------------> FUTURE
TRAIN DATA                       TEST DATA
```

For a next-hour prediction:

```
information available at t
        ↓
prediction for t + 1
```

## Main lesson

The validation process should reproduce the direction of time.

Useful approaches include:

- chronological train/test split
- walk-forward validation
- rolling training windows
- evaluating different volatility regimes

This is especially important for BTC because market behavior changes over time.
