# ML Model Selection Notes

The BTC project compared different model families while studying how well they captured the next-hour volatility target.

The main lesson was not simply "choose the model with the highest score."

A useful model-selection process asks:

1. Is the validation setup realistic?
2. Are the features leakage-free?
3. How does the model perform on normal periods?
4. How does it behave during extreme volatility?
5. Does the model generalize to future market regimes?
6. Can the model be deployed reliably?

For a portfolio project, documenting these decisions is as important as reporting the final metric.
