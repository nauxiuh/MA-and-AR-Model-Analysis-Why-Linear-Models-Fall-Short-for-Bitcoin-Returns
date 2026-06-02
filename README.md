# MA and AR Model Analysis: Why Linear Models Fall Short for Bitcoin Returns

This project works through the theory of MA(2) and AR(2) time series models and then tests whether Bitcoin returns actually exhibit the kind of linear structure these models require. The short answer is no, and the ACF and PACF plots make that clear. The longer answer is that this result is itself informative: Bitcoin returns behave like white noise in terms of linear dependence, which is consistent with market efficiency, but the volatility is far from random, which is why this project sets the stage for GARCH-type modeling.

## Repository Structure

```
├── FinancialDataAnalysis.ipynb             # Main analysis notebook
└── data/
    └── bitcoin_data.csv                    # Bitcoin adjusted close prices
```

## Analysis

### Part 1: MA(2) Process

An MA(q) model expresses the current value as a weighted sum of current and past error terms:

```
X_t = mu + eps_t + a1*eps_{t-1} + a2*eps_{t-2}
```

**Theoretical properties of MA(2):**

All finite-order MA processes are stationary by construction. The mean and variance do not depend on time, so no stationarity condition on the parameters is needed.

Invertibility requires the roots of the MA polynomial to lie outside the unit circle, which gives three conditions: |a2| < 1, a2 + a1 < 1, and a2 - a1 < 1.

The moments work out to:
- Mean: mu
- Variance: sigma^2 * (1 + a1^2 + a2^2)
- ACF cuts off exactly at lag 2: rho_k = 0 for k > 2

That last point is the key diagnostic. If the data follows an MA(q) process, the ACF should drop sharply to zero after lag q and stay there.

**What the Bitcoin data shows:**

The ACF for Bitcoin log returns shows no meaningful spikes at any lag. rho_1 is approximately -0.045 and rho_2 is approximately 0.048, both right at the edge of the 95% confidence bound of +/- 2/sqrt(N) ~ 0.04. There is no clear cutoff, no pattern, nothing to suggest a finite MA order. An MA(q) model is not appropriate here.

### Part 2: AR(2) Process

An AR(p) model expresses the current value as a linear combination of its own past values:

```
X_t = c + b1*X_{t-1} + b2*X_{t-2} + eps_t
```

**Theoretical properties of AR(2):**

All finite-order AR processes are invertible by definition.

Stationarity requires the roots of the characteristic polynomial to lie outside the unit circle, giving: b1 + b2 < 1, b2 - b1 < 1, and |b2| < 1.

The moments:
- Mean: c / (1 - b1 - b2)
- Variance: (1 - b2) * sigma^2 / [(1 + b2) * ((1 - b2)^2 - b1^2)]
- ACF decays geometrically: rho_k = b1 * rho_{k-1} + b2 * rho_{k-2} for k >= 2

The key diagnostic for AR models is the PACF, not the ACF. An AR(p) process has PACF that cuts off sharply after lag p, while the ACF decays slowly and smoothly.

**What the Bitcoin data shows:**

The PACF values at lags 1 and 2 are approximately -0.045 and 0.046, both within the white noise confidence bound. There is no sharp cutoff in the PACF, and there is no geometric decay pattern in the ACF. Neither signature of an AR process is present in the data. An AR(p) model is not appropriate for Bitcoin returns either.

### Conclusion

Both the ACF and PACF analysis point to the same thing: Bitcoin log returns are essentially white noise in terms of linear serial correlation. Past returns do not help predict future returns, which is what weak-form market efficiency would predict.

This does not mean the series is truly random. Earlier analysis (project 2) showed strong autocorrelation in absolute and squared returns, which is evidence of volatility clustering. The linear structure that MA and AR models capture just does not exist in returns. The nonlinear dependence in variance is what needs to be modeled, and that is what GARCH models are designed to do.

## Key Takeaways

- MA(q) processes are always stationary. Their signature is ACF that cuts off after lag q.
- AR(p) processes are always invertible. Their signature is PACF that cuts off after lag p.
- Bitcoin returns show neither pattern. ACF and PACF are both flat and within noise bounds.
- This rules out linear models like MA and AR for the return series itself.
- The dependence that does exist in Bitcoin lives in the volatility, not the level of returns.

## Dependencies

```bash
pip install numpy pandas matplotlib statsmodels
```

## Usage

Rename the data file to `bitcoin_data.csv`, then run:

```bash
jupyter notebook FinancialDataAnalysis3.ipynb
```
