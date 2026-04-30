# Bayesian Kelly Criterion: From Point Estimate to Probability Distribution

> Four scenarios showing why treating your edge as uncertain produces better position sizing than a single-number Kelly estimate.

**Author:** Patrick Lefler

**Published:** March 17, 2026

**Rendered:** https://patrick-lefler.github.io/rQuarto_kelly_criterion_modeling_project_2026-03-17/

---

## Project Introduction

> Replaces the standard Kelly Criterion's fixed win-probability assumption with a Beta-distributed posterior, generating a full distribution of recommended stakes — sized conservatively by evidence quality, not confidence alone.

---

## Overview

The standard Kelly Criterion converts a win-probability estimate directly into a position size — a translation that ignores the noise inherent in any finite sample. This project treats win probability as a Beta-distributed random variable, updating a prior with observed wins and losses via Bayes' theorem to produce a posterior distribution over plausible edge values. Applying the Kelly formula to 10,000 draws from that posterior yields a distribution of recommended stakes rather than a single number. The 25th percentile of that distribution (p25) serves as the default stake: optimal if the true win rate falls anywhere in the upper three-quarters of the uncertainty range. Four scenarios trace the framework's behavior from sparse data through rich evidence, and conclude with a stress test showing that conservative sizing survives model optimism while full Kelly does not.

---

## Tech Stack

- **Language:** R
- **Framework:** [Quarto](https://quarto.org/)
- **Primary Libraries:** ggplot2, plotly, dplyr, tidyr, knitr, kableExtra, scales, htmltools, sessioninfo
- **Deployment / Output:** Self-contained HTML Document

---

## Repository Structure

```
├── data/               # No external data required (all scenarios are simulated)
├── scripts/            # Helper R scripts
├── output/             # Rendered HTML files
├── _brand.yml          # Brand configuration
└── index.qmd           # Main Quarto entry point
```

---

## Key Findings

**Sparse data produces near-zero conservative stakes — correctly.** With only 12 observations and a 58% win rate, the traditional Kelly formula recommends a confident 16% stake. The Bayesian framework's p25 recommendation is zero: not because the edge is absent, but because the posterior is too wide to distinguish a genuine edge from sampling noise. The overconfidence premium — the gap between p50 and p25 Kelly recommendations — is 13 percentage points, a direct measure of how much an analyst over-stakes by ignoring estimation uncertainty.

**Evidence earns the right to size up; it cannot be assumed.** Scenarios 1 through 3 use comparable win rates (56%–64%) but increasing sample sizes (12, 25, and 50 trials). The p25 recommendation moves from zero to 8% to 20% as evidence accumulates — not because the strategy improves, but because the posterior tightens. By Scenario 3, the overconfidence premium has compressed to 8.9 percentage points and p25 has converged toward p50. The framework is self-correcting: caution is a function of uncertainty, not disposition.

**A 4-point model overstatement compounds into near-ruin at full Kelly.** Scenario 4 uses identical inputs to Scenario 1 but simulates outcomes at a true win rate of 52% rather than the posterior mean of 56%. Full Kelly — sized for a 56% world — produces a median terminal bankroll of $50,466 after 200 bets and a 90.5% median drawdown. The p25 stake, already near zero because the posterior was wide, preserves capital almost entirely. The overconfidence premium is not a theoretical curiosity; it is the cost of misplaced precision.

---

## License

This project is licensed under the MIT License. See the `LICENSE` file for details.

---

## Contact

Patrick Lefler — [LinkedIn](https://www.linkedin.com/in/patricklefler/) | [GitHub Pages](https://patrick-lefler.github.io) | [Substack](https://substack.com/@pflefler)
