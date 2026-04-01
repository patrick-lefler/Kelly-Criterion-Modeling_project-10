# Bayesian Kelly: From Point Estimate to Probability Distribution

**Author:** Patrick Lefler  </br>
**Published:** 2026-03-17 </br>
**Project #:** Project-10 </br>
**Tools:** R · Quarto · Plotly · ggplot2 · kableExtra

---

## Overview

The Kelly Criterion holds a unique spot in finance. It is often cited, misused, and rarely tested against its own assumptions. The formula works best when the win probability is known exactly. In reality, win probabilities come from limited historical data, which can be noisy. For example, a strategy that won 58% of its last 12 trades might really have a true win rate anywhere from 40% to 75%. The standard Kelly calculation ignores this uncertainty. It turns a noisy estimate into a confident position size. 

This project says that direct translation risks Kelly-based sizing. It can lead to serious, unmeasured issues. The proposed method treats win probability as a Beta-distributed random variable, not a fixed number. Wins and losses update a prior distribution using Bayes' theorem. This produces a posterior that shows both the evidence and remaining uncertainty. By applying the Kelly formula to 10,000 draws from this posterior, we get a range of recommended stakes. This range is broad with weak evidence and narrow with strong evidence. This results in a cautious, percentile-based policy. 

The 25th percentile of this distribution, called p25, is the suggested default stake. It’s optimal if the true win rate falls within the upper three-quarters of the uncertainty range. Four scenarios demonstrate the practical effects of this framework. With just 12 observations, the posterior is too broad. It can’t confidently support a 16% stake based on a point estimate. Kelly responds with a near-zero size at the conservative percentile. 

The second and third scenarios show that as more evidence comes in, the p25 recommendation moves closer to full Kelly. This illustrates the framework’s self-correcting nature. The fourth scenario is crucial. It uses the same inputs as the first but simulates outcomes at a true win rate of 52%, rather than the posterior mean of 56%. This small difference highlights how in-sample models can overstate out-of-sample performance. Full Kelly is heavily punished, while the p25 stake remains intact. The overconfidence premium is the gap between median and conservative Kelly recommendations. This gap helps risk managers and investment committees see how reliable an edge estimate is."

---

## Key concepts

**Bayesian updating** — Prior beliefs about the win probability are updated using observed wins and losses via Bayes' theorem. For a Binomial likelihood with a Beta prior, the posterior is analytically tractable: Beta(α + wins, β + losses).

**Kelly fraction distribution** — Rather than computing a single Kelly recommendation, 10,000 values of the win probability are drawn from the posterior. Each produces a Kelly fraction. The resulting distribution reveals the full range of defensible stakes given the available evidence.

**Percentile-based staking** — The p25 Kelly fraction — the stake that is optimal if the true win rate is anywhere in the upper 75% of the uncertainty range — is proposed as the recommended default. The gap between p25 and p50 (the overconfidence premium) quantifies the cost of treating an estimated probability as a known one.

**Scenario 4: Optimism meets reality** — The most important scenario in the document. Identical inputs to Scenario 1, but the wealth simulation uses a true win rate of 52% rather than the posterior mean of 56.3%. This is the out-of-sample test: full Kelly degrades badly; the p25 stake survives.

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
library(plotly)
library(dplyr)
library(tidyr)
library(ggplot2)
library(knitr)
library(kableExtra)
library(scales)
library(htmltools)
library(sessioninfo)
```

Quarto 1.4 or later. Install from [quarto.org](https://quarto.org).

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
