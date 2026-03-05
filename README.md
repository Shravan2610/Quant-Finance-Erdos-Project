# Delta Hedging Performance under GARCH(1,1) Dynamics

This project investigates the limitations of the **Black-Scholes delta-hedging** strategy when the underlying asset's price follows a **GARCH(1,1) process** rather than standard Geometric Brownian Motion. Specifically, we analyze how the ARCH parameter ($\alpha$) influences hedging errors and portfolio P&L drift.

---

## 1. Motivation
The Black-Scholes model assumes constant volatility, which is rarely observed in real-world financial markets. Markets exhibit "volatility clustering," where large price movements tend to be followed by other large movements. By simulating a GARCH environment, we can test the robustness of "Greeks" derived from a model that ignores these dynamics. Understanding the impact of the $\alpha$ parameter (the "shock" sensitivity) is crucial for risk managers who must account for fat-tailed return distributions and sudden volatility spikes.

## 2. Introduction
In this project, we simulate 10,000 asset price paths using a GARCH(1,1) model. We then attempt to maintain a delta-neutral position for a European Call option. Even though the "true" world in our simulation is GARCH, the hedge is executed using the standard Black-Scholes delta, re-calculating the delta daily based on the current instantaneous GARCH volatility.

## 3. Method Setup
The simulation is built using the following parameters:
* **Initial Stock Price ($S_0$):** 250
* **Strike Price ($K$):** 240
* **Time to Maturity ($T$):** 252 Days (1 Year)
* **Risk-free Rate ($r$):** 0.0
* **GARCH Parameters:** * $\omega$ (Omega): $1 \times 10^{-6}$
    * $\beta$ (Beta): 0.8
    * $\alpha$ (Alpha) List: [0.0, 0.02, 0.10, 0.15, 0.20]
* **Monte Carlo Trials:** 10,000

## 4. Methodology

### GARCH Path Generation
The variance ($h_t$) of log-returns at time $t$ is defined by the recursive formula:
$$h_t = \omega + \alpha r_{t-1}^2 + \beta h_{t-1}$$



### Delta Hedging Strategy
Daily rebalancing is performed using the Black-Scholes Call Delta:
$$\Delta_t = N(d_1(S_t, \sigma_{ann, t}))$$
Where $\sigma_{ann, t}$ is the annualized GARCH volatility ($\sqrt{h_t \times 252}$). The portfolio value is tracked as the sum of the option value, the stock position, and the cash component.

## 5. Results
The simulation shows a clear relationship between the $\alpha$ parameter and the instability of the hedged portfolio.

### Summary Statistics
| $\alpha$ | Mean Final P&L | Std Dev P&L | RMSE |
| :--- | :--- | :--- | :--- |
| **0.00** | 10.54 | 0.50 | 10.55 |
| **0.10** | 11.44 | 0.81 | 11.47 |
| **0.20** | 22.14 | 14.20 | 26.31 |

*(Data source: Internal Simulation Results)*

### Visualizing the Drift
As $\alpha$ increases, the mean portfolio value deviates significantly from the initial hedge expectation.



* **$\alpha = 0.0$**: Results in the most stable mean portfolio value, representing a regime with minimal volatility risk.
* **$\alpha = 0.2$**: Shows significant positive drift and instability.

## 6. Conclusion
The findings lead to two primary conclusions regarding the risk of "volatility clustering" in hedging:

1. **Return Distributions**: Higher $\alpha$ makes the stock price process look like fatter tails and higher peaks in returns, meaning there is a greater chance of both very small and very large price movements.
2. **Hedging Failure**: When $\alpha$ is high, volatility can spike dramatically after a large price move. The Black-Scholes delta used for hedging is a static sensitivity derived under constant volatility. It fails to account for the risk that volatility will change or be miscalculated due to sudden volatility shifts, leading to P&L errors and large positive drift.
