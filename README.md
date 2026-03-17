# Bayesian Kelly: From Point Estimate to Probability Distribution

**Author:** Patrick Lefler  
**Published:** March 2026  
**Tools:** R · Quarto · Plotly · ggplot2 · kableExtra

---

## Overview

The Kelly Criterion is one of the most cited position-sizing frameworks in quantitative finance. Its central weakness is also its most overlooked assumption: it treats the win probability as a known quantity. In practice, win probabilities are estimated from finite samples of data — and finite samples carry uncertainty.

This project replaces the single point estimate with a Beta-distributed posterior distribution over the win probability, propagates that uncertainty through the Kelly formula, and derives a percentile-based staking policy that is both mathematically principled and practically conservative. The result is a framework that tells you not just *what* Kelly recommends, but *how confident the evidence makes that recommendation* — and how much you are overstaking if you ignore the uncertainty.

The document walks through four scenarios that tell a progressive story: from sparse data where Kelly correctly recommends near-zero stakes, through the accumulation of evidence that grants permission to size up, to an out-of-sample stress test that demonstrates why conservative sizing survives model optimism.

---

## Key concepts

**Bayesian updating** — Prior beliefs about the win probability are updated using observed wins and losses via Bayes' theorem. For a Binomial likelihood with a Beta prior, the posterior is analytically tractable: Beta(α + wins, β + losses).

**Kelly fraction distribution** — Rather than computing a single Kelly recommendation, 10,000 values of the win probability are drawn from the posterior. Each produces a Kelly fraction. The resulting distribution reveals the full range of defensible stakes given the available evidence.

**Percentile-based staking** — The p25 Kelly fraction — the stake that is optimal if the true win rate is anywhere in the upper 75% of the uncertainty range — is proposed as the recommended default. The gap between p25 and p50 (the overconfidence premium) quantifies the cost of treating an estimated probability as a known one.

**Scenario 4: Optimism meets reality** — The most important scenario in the document. Identical inputs to Scenario 1, but the wealth simulation uses a true win rate of 52% rather than the posterior mean of 56.3%. This is the out-of-sample test: full Kelly degrades badly; the p25 stake survives.

---

## Scenarios

| | Scenario | Story | Wins / Trials | Posterior mean |
|---|---|---|---|---|
| 1 | The Danger Zone | Sparse data — point estimate misleads | 7 / 12 | 56.3% |
| 2 | The Transition | p25 turns meaningfully positive | 15 / 25 | 60.0% |
| 3 | The Experienced Operator | p25 converges toward full Kelly | 32 / 50 | 64.3% |
| 4 | Optimism Meets Reality | True edge lower than estimated — full Kelly near-ruins | 7 / 12 | 56.3% (true: 52%) |

All scenarios use decimal odds of 2.0 (even money, 50% break-even threshold) and a $100,000 starting bankroll.

---

## Repository structure

```
bayesian-kelly/
├── bayesian_kelly_scenarios.qmd   # Main Quarto document
├── _brand.yml                     # Brand colours and typography
├── README.md                      # This file
└── docs/                          # Rendered HTML output (GitHub Pages)
    └── index.html
```

---

## Reproducing the analysis

### Prerequisites

R 4.3 or later with the following packages:

```r
install.packages(c(
  "plotly",
  "dplyr",
  "tidyr",
  "ggplot2",
  "knitr",
  "kableExtra",
  "scales",
  "htmltools",
  "sessioninfo"
))
```

Quarto 1.4 or later. Install from [quarto.org](https://quarto.org).

### Rendering

```bash
quarto render bayesian_kelly_scenarios.qmd
```

The output is a fully self-contained HTML file. No server or internet connection is required to view the rendered document.

### Rendering with custom parameters

The document does not use Quarto params for the scenario definitions — all four scenarios are defined as named lists in the setup chunk and can be modified directly. To change the win rate, sample size, prior, or odds for any scenario, edit the `scenarios` list in the setup chunk:

```r
scenarios <- list(
  s1 = list(
    alpha_prior = 2, beta_prior = 2,
    wins = 7, n_trials = 12,
    b_odds = 2.0,
    true_p = NULL          # NULL = draw from posterior
  ),
  ...
)
```

---

## Analytical pipeline

Each scenario passes through five stages:

1. **Posterior update** — Beta(α, β) prior + Binomial likelihood → Beta(α + wins, β + losses) posterior
2. **Density overlay** — Prior, scaled likelihood, and posterior plotted together via `ggplotly()`
3. **Kelly distribution** — 10,000 posterior draws each produce an f* value via `kelly_fraction(p, b)`
4. **Sensitivity table** — Five staking strategies (p10, p25, p50, p75, Fixed 2%) simulated over 200 bets across 2,000 runs
5. **Wealth trajectories** — Median paths and IQR bands over 500 bets, plus terminal bankroll histogram

---

## Design standards

- **Theme:** Quarto Sandstone (Bootswatch)
- **Brand:** Custom palette defined in `_brand.yml` — off-white background, dark grey text, blue/red/green accent ramps
- **Typography:** Roboto (Google Fonts, via `_brand.yml`)
- **Visualizations:** `ggplotly()` for density overlays, `plot_ly()` / `add_bars()` for Kelly histograms and trajectory panels
- **Tables:** `kbl()` + `kable_styling()` with striped, hover, condensed, responsive bootstrap options
- **No Shiny, no OJS** — document renders to a standalone HTML file that works from the filesystem or any web host

---

## Related projects

This is Project 1 in a three-part series on Bayesian position sizing:

- **Project 1 (this document):** Bayesian Kelly under single-asset uncertainty
- **Project 2 (planned):** Multi-asset Kelly — portfolio allocation with correlated returns
- **Project 3 (planned):** Regime-aware Kelly — GARCH-EVT volatility regimes and tail-adjusted sizing

---

## License

MIT License. You are free to use, adapt, and republish this analysis with attribution.

---

*Rendered with [Quarto](https://quarto.org/) · Packages: `plotly`, `dplyr`, `tidyr`, `ggplot2`, `knitr`, `kableExtra`, `scales`, `htmltools`, `sessioninfo`*
