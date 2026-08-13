# WEZ Hunting — Weapon Engagement Zone Ballistics Simulator

A single-file, browser-based exterior ballistics simulator for evaluating hit probability on hunting-sized targets under realistic uncertainty (muzzle velocity SD, rifle dispersion, wind, range/angle estimation error). Built around G1/G7 drag models and a Monte Carlo shot-distribution engine, with a live target-hit visualization and miss-cause (Pareto) breakdown.

No build step, no dependencies to install — it's a single `.html` file that runs entirely client-side.

## Features

- **G1 / G7 drag models** — point-mass exterior ballistics with velocity-banded retardation tables, selectable per bullet/BC source
- **Monte Carlo Weapon Engagement Zone (WEZ) analysis** — simulates shot outcomes across configurable iteration counts (500–2500) to estimate Probability of Hit (P<sub>hit</sub>)
- **Rifle dispersion from group data** — converts a measured group extreme spread (ES) and shot count into an estimated circular 1σ dispersion via a finite-sample order-statistic model, instead of a fixed ES/2.2 rule of thumb
- **Muzzle velocity SD** as an explicit input, propagated through the trajectory solve rather than assumed
- **Full 3-D crosswind integration** — wind is applied via relative-air-velocity integration through the step solver rather than a closed-form windage-lag approximation
- **Environmental correction** via density altitude (ICAO/ISA lapse-rate density, referenced against the historical Army Standard Metro atmosphere the G1–G8 tables were calibrated on)
- **Zero-angle solver** — bisection solve for the bore angle that zeros the selected load at a given range and sight height
- **Target/vital-zone presets** — roe deer vitals, 50×50 cm plate, IPSC silhouette, or custom dimensions
- **Miss-cause breakdown (Pareto chart)** — attributes misses to contributing error sources
- **Vertical correction output** in mrad plus drop, for direct dial-in
- **Mobile-friendly layout** with iOS Safari zoom-on-focus fix for form inputs

## Usage

Just open `WEZHunting.html` in a browser — locally, from a static file host, or via GitHub Pages. Enter your load data (BC, drag model, muzzle velocity and its measured SD), a group ES from a known shot count and distance, environmental conditions, target/range, and run the simulation.

```bash
# clone and open locally
git clone https://github.com/<your-username>/WEZHunting.git
cd WEZHunting
open WEZHunting.html   # or just double-click the file
```

Or enable **GitHub Pages** (Settings → Pages → deploy from branch) to host it directly.

## How it works

The ballistic engine is a from-scratch JavaScript port of the G1/G7 retardation tables and step integrator used by pyBallistics / GNU Ballistics (SI-unit conversion, density-ratio scaling), verified coefficient-for-coefficient against `pyBallistics.drag.G1()` / `.G7()`. Unlike those references, wind is handled with a full 3-D relative-air integration rather than a closed-form windage-lag approximation — see the inline code comments for the derivation and rationale of each deviation.

The Monte Carlo loop draws per-shot muzzle velocity (from the entered SD), rifle dispersion (from the group-ES model), and range/wind estimation error, runs each through the trajectory solver, and classifies the result as a hit or miss against the selected target box — producing an empirical P<sub>hit</sub> and a breakdown of which error source drove each miss.

## Validation

Trajectory output has been cross-checked against a Kestrel unit running Applied Ballistics. Deviations from reference ballistic tools (pyBallistics, GNU Ballistics) are intentional and documented — see the code comments flagged in the UI (e.g. crosswind handling, atmosphere reference).

## Disclaimer

This tool is provided for educational and load-development planning purposes only. It performs an approximate numerical simulation of a complex physical process. **Do not use it as the sole basis for a live-game shot/no-shot decision.** Always verify trajectory and wind calls against a validated ballistic solver and your own chronograph/group data, and apply your own ethical range limits and local hunting law. Vital-zone dimensions shown are planning approximations, not anatomical references.

## License

**GPL-3.0**

## Acknowledgements

- G1/G7 retardation models and step-integrator approach informed by [pyBallistics](https://github.com/bradrisse/pyBallistics) and the original [GNU Ballistics Library](https://sourceforge.net/projects/ballisticslib/)
- Charting via [Chart.js](https://www.chartjs.org/)
- Styling via [Tailwind CSS](https://tailwindcss.com/)
