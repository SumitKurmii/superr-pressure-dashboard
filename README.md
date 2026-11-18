# Who Delivers Under Pressure? A Leverage-Based Cricket Analytics Dashboard

An interactive dashboard that ranks the **top 10 batters and bowlers in high-pressure moments** using IPL and T20I ball-by-ball data from 2022 to 2026. Built for the **SupeRR Selector Season 2, Round 1** challenge on Unstop.

**Live demo:** https://superr-pressure-dashboard-sigma.vercel.app

## The problem

Cricket fans and selectors often talk about who is "clutch", but most simple measures (late-innings strike rate, death-over economy) mostly measure *when* a player bats or bowls, not how well they perform when the match is genuinely on the line. The goal was to define pressure from the match itself and rate players fairly, with uncertainty attached.

## Approach

1. **Data preparation.** Joined six CSVs (ball-by-ball, matches, competitions, teams, playing XI, player mapping) covering about 200,000 deliveries. Verified conventions against reported results (run margins, wicket margins, legal-ball rules), rebuilt match state before every delivery, excluded super overs, DLS-affected matches without revised targets, and 53 associate-nation matches. Rating scope: 780 matches.
2. **Pressure definition.** Trained a monotone gradient-boosting win-probability model (chase AUC 0.89, well calibrated) and derived a **leverage index**: the win-probability swing between the next ball being a six and being a wicket, relative to the league average. A ball is a *pressure ball* when leverage is at least 1.5 and win probability is between 10% and 90%. This covers 17.9% of all balls and 29% of chase balls, and pressure balls move win probability 4.2 times more per ball than ordinary balls in chases.
3. **Run value per ball.** Runs plus the change in expected remaining runs, so wickets and wasted balls are valued in the same units. Compared against the league in the same match state, which removes the phase effect (for example, death-over batters getting credit just for batting late).
4. **Usual level vs pressure.** A two-level empirical-Bayes model estimates each player's usual level on non-pressure balls and a shrunk pressure shift, so small samples get little weight.
5. **Ranking.** Players are ranked by a reliable floor: the 5th percentile of the posterior estimate over 1,000 bootstrap resamples of whole matches.
6. **Robustness.** Tested 13 sensitivity scenarios (leverage thresholds, closeness gates, chases only, IPL only, T20I only, sample rules, evidence gate) and a split-half repeatability check.

## Key findings

- The strongest evidence is for the **group**, not the exact order. The top 3 batters and top 3 bowlers stay in the top 10 in 82 to 99% of resamples, while places 7 to 10 are close calls.
- **Pressure-specific skill is small.** What repeats is general quality shown in pressure situations, and the model reports this honestly instead of rewarding lucky spells.
- A baseline draft solution (using a simple late-overs flag) was found to be structurally biased: its flagged balls have average leverage of 0.99 (same as any ball), and 86% of all batters appear to "improve" under its flag purely because of phase.

## Dashboard features

- Batters and bowlers toggle
- Forest plot showing estimate, usual level and 5th to 95th percentile range
- Sortable table with expandable raw stats (strike rate, economy, dot-ball %, IPL/T20I split)
- Pressure-definition comparison charts and sensitivity analysis
- Light and dark mode, responsive layout, no external dependencies

## Tech stack

- **Analysis:** Python (pandas, numpy, scikit-learn), gradient boosting, empirical Bayes, bootstrap resampling
- **Dashboard:** single-file HTML, CSS and vanilla JavaScript with inline SVG charts
- **Deployment:** GitHub and Vercel

## Repository structure

```
index.html   # self-contained dashboard (data embedded)
README.md
```

## Run locally

No build step. Open `index.html` in a browser, or serve it:

```
python -m http.server 8000
```

## Limitations

- Pressure and the 1.5 leverage threshold are analyst choices; places 7 to 10 move under other settings.
- First-innings win probability is weak (no venue, pitch or team strength), so first-innings pressure is under-identified.
- No adjustment for opposition quality, venue, season or batting order beyond match state and format.
- Samples are modest (median top-10 pressure sample is about 260 balls), so intervals matter more than exact order.
- Rankings describe past performance only, not availability, fitness, price or squad fit.

## Author

**SUMIT**
[LinkedIn](https://linkedin.com/in/YOUR-PROFILE) | [GitHub](https://github.com/YOUR-USERNAME)
