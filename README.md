# AAPL Options Modeling: Greeks & Monte Carlo

This repository contains Python implementations and mathematical explanations for real-time option pricing challenges discussed in my recent interview.

## 1. Real-Time Delta and Vega Calculation
When dealing with live market feeds for thousands of options, relying on static parameter freezing is insufficient. I explored two alternatives:

### Alternative 1: Implied Volatility Surface Calibration
In the live market, we do not know the true volatility ($\sigma$). We observe the market price.
* **Method:** We use a root-finding algorithm (e.g., Newton-Raphson) to find the volatility value that makes the Black-Scholes theoretical price equal to the live Market Mid-Price.
* **Benefit:** This anchors our Greeks (Delta, Vega) to the market's actual expectation of risk rather than historical data.

### Alternative 2: Taylor Series Approximation
For high-frequency updates, full model recalculation is too slow. We use a Taylor expansion to approximate the new price based on sensitivities calculated in the previous "tick":
$$P_{new} \approx P_{old} + \Delta \cdot (S_{new} - S_{old}) + \frac{1}{2} \Gamma \cdot (S_{new} - S_{old})^2$$
* **Benefit:** Extremely low latency updates.
* **Limitation:** Accumulates error over large price moves; requires periodic "re-centering" (recalculating the full model).

## 2. Monte Carlo Simulation (Geometric Brownian Motion)
### The "Positive Price" Problem
A standard random walk ($S_t = S_{t-1} + \text{shock}$) allows asset prices to drop below zero, which is impossible for equities like AAPL.

### The Solution: GBM
We model the stock price using **Geometric Brownian Motion**, which treats the returns as log-normally distributed.
$$S_t = S_0 \cdot \exp \left( (\mu - \frac{1}{2}\sigma^2)t + \sigma W_t \right)$$
Because the exponential function $f(x) = e^x$ has a range of $(0, \infty)$, the simulated price $S_t$ is strictly positive for all real values of the shock $W_t$.

### Simulation Parameters (AAPL)
* **Spot ($S_0$):** $255.78
* **Strike ($K$):** $260.00
* **Volatility ($\sigma$):** 28%
* **Drift ($\mu$):** 12%
* **Simulations:** 10,000 paths over 252 trading days.