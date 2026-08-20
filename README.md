# Observation-masked neural posterior estimation (OM-NPE)

Code to reproduce the figures and tables in:

> Retkute R. & Gilligan C.A. **Observation-masked neural posterior estimation for
> heterogeneous epidemiological surveillance data.**
> https://doi.org/10.64898/2026.08.03.742452

OM-NPE is a simulation-based Bayesian inference framework for surveillance data
that is sparse, irregularly timed and heterogeneous across observational units.
Each record is represented on a common temporal grid together with a binary mask
recording which grid points were actually observed, so that a single amortised
neural posterior estimator can be applied across units with different observation
schedules. Once trained, the estimator produces posterior distributions in under
a second, without retraining or MCMC sampling.

## Contents

| File | Case study |
|---|---|
| `01_zika_french_polynesia.ipynb` | Zika virus, six French Polynesian archipelagos, weekly sentinel surveillance, 2013–14 |
| `02_xylella_fastidiosa.ipynb` | *Xylella fastidiosa*, 17 olive groves in Apulia, annual disease-severity surveys, 2014–18 |
| `requirements.txt` | Python dependencies |

Each notebook is self-contained and runs top to bottom: simulate, train once,
then evaluate the amortised posterior for every observational unit.

## Data

Both notebooks read from a `data/` directory:

```
data/S1_Dataset.csv          # weekly Zika sentinel counts and active-site counts per archipelago
data/Xf_epi.csv              # per-grove Xylella disease proportions by year and state
data/Xf_number_trees.csv     # number of trees surveyed per grove
```

The Zika surveillance data are from Kucharski *et al.* (2016), *PLOS Neglected
Tropical Diseases* **10**, e0004726. The *Xylella* survey data are from White
*et al.* (2020), *Plant Pathology* **69**, 1403–1413.

## Installation

```bash
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
jupyter lab
```

## Reproducing the paper

Run each notebook in order. Approximate single-CPU runtimes:

| Stage | Zika | *Xylella* |
|---|---|---|
| Simulation + training (10⁶ simulations) | ~90 min | ~45 min |
| Posteriors for all observational units | < 1 s | < 1 s |
| Sequential / schedule-optimisation analyses | ~2 min | ~3 min |
| Calibration diagnostics (SBC + TARP, 10⁴ trials) | ~15 min | ~20 min |

Both notebooks expose a `QUICK` flag at the top. Setting `QUICK = True` shrinks
the training and diagnostic budgets so the whole notebook runs in a few minutes;
results will not match the paper.

Each notebook caches its trained estimator (`zika_density_estimator.pt`,
`xf_npe_density_estimator.pkl`) so the analysis sections can be re-run without
repeating training.

### Outputs

**Notebook 1 — Zika**

| Output | Paper item |
|---|---|
| `figure1.pdf` | Figure 1 — posterior-predictive fits, six archipelagos |
| `figure2.pdf` | Figure 2 — sequential inference of *R*₀ |
| `table3.csv` | Table 3 — *R*₀ compared with Kucharski *et al.* (2016) |
| `figure_si1.pdf` | SI Figure 1 — posteriors vs population size |
| `figure_si2a.pdf`, `figure_si2b.pdf` | SI Figure 2 — SBC rank CDFs and TARP coverage |

**Notebook 2 — *Xylella fastidiosa***

| Output | Paper item |
|---|---|
| `figure3a.pdf` | Figure 3A — posterior-predictive fits, 17 groves |
| `figure3b.pdf` | Figure 3B — predicted state proportions at year 16 |
| `figure3c.pdf` | Figure 3C — forecast variance vs extrapolation horizon |
| `figure4a.pdf` | Figure 4A — all 127 survey schedules ranked by forecast variance |
| `figure4b.pdf` | Figure 4B — best schedule at each observation count |
| `table4.csv` | Table 4 — parameters compared with White *et al.* (2020) |
| `figure_si3.pdf` | SI Figure 3 — posteriors vs grove size |
| `figure_si4a.pdf`, `figure_si4b.pdf` | SI Figure 4 — SBC rank CDFs and TARP coverage |



## Reproducibility

Random seeds are set at the top of each notebook. The number of
posterior draws per record is `N_POST`, set in the configuration cell.


```bibtex
@article{RetkuteGilligan_OMNPE,
  author  = {Retkute, Renata and Gilligan, Christopher A.},
  title   = {Observation-masked neural posterior estimation for heterogeneous
             epidemiological surveillance data},
  doi     = {10.64898/2026.08.03.742452},
  url     = {https://doi.org/10.64898/2026.08.03.742452}
}
```
