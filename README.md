# Dutch Precipitation Catastrophe Model

**Probabilistic nat cat model** | GEV hazard · Copula dependence · Sigmoid vulnerability · Monte Carlo simulation

This repository contains a fully reproducible catastrophe model for precipitation risk
at five locations in the Netherlands, structured as a Quarto document. It demonstrates
the core workflow of a probabilistic nat cat model — from hazard characterisation to
portfolio-level risk metrics — using methods directly applicable to the insurance and
reinsurance industry.

This project is a direct extension of [my thesis practical chapter]
(https://risam0arek.github.io/precipitation-extremes-gev-copula/),
*Extreme Precipitation in the Netherlands: GEV Modelling & Extreme Value Copulas*,
which covers the hazard and dependence analysis in detail. The GEV parameters and
5D copula are loaded from that chapter without re-estimation.

---

## Live report

👉 https://risam0arek.github.io/precipitation-catastrophe-model/

---

## Model Structure

The model follows the standard four-component framework used in commercial cat models
(RMS, Verisk AIR, PERILS):

| Component | Implementation |
|-----------|----------------|
| **Hazard** | GEV distributions fitted to observed annual maximum precipitation at five Dutch meteorological stations; spatial dependence via 5D Gumbel copula |
| **Frequency** | Poisson process, $\lambda$ = 0.5 events per year |
| **Vulnerability** | Sigmoid damage functions, two building classes per location |
| **Exposure** | Synthetic portfolio (EUR), five model locations |

---

## What's in the Analysis

| Section | Content |
|---------|---------|
| Model Architecture | Four-component framework, full simulation pipeline with notation |
| Portfolio | Synthetic insured values by location and building class |
| Vulnerability Functions | Sigmoid parameterisation, baseline correction, curves per class |
| Simulation | Event loss and annual loss functions, 10,000-year Monte Carlo |
| Risk Metrics | AAL, VaR 99%, VaR 99.5%, TVaR 99% |
| Annual Loss Distribution | Histogram with risk metric overlays |
| OEP vs AEP Curves | Occurrence and aggregate exceedance probability curves |
| Model Limitations | Hazard metric, copula choice, frequency, vulnerability, exposure |

---

## Key Output

```
AAL       : EUR 0.266 bn   — long-run expected annual loss
VaR 99%   : EUR 3.295 bn   — 1-in-100 year loss
VaR 99.5% : EUR 4.580 bn   — 1-in-200 year loss (Solvency II SCR reference)
TVaR 99%  : EUR 5.395 bn   — mean loss conditional on exceeding VaR 99%
```

---

## Connection to the Thesis Practical Chapter

The hazard component of this model — GEV distributions and the 5D Gumbel copula —
is estimated in 
[my thesis practical chapter](https://risam0arek.github.io/precipitation-extremes-gev-copula/). 
That chapter shows that copula-implied 
joint exceedance probabilities are **14× to 98× higher** than under independence
for station pairs. This amplification carries directly into the loss distribution
tail here, and therefore into the VaR and TVaR estimates that drive reinsurance
structuring and capital requirements.

The 5D Gumbel copula is used not because it is the best-fitting family, but because
it is the only extreme value copula available for dimensions greater than 2 in R's
`copula` package. The thesis practical chapter shows that bivariate AIC selection
favours Hüsler–Reiss (6 of 10 pairs) and t-EV (3 of 10 pairs). A vine copula would
resolve this limitation and is noted as a natural extension.

---

## Reproducing the Analysis

**Requirements:** R ≥ 4.3, Quarto ≥ 1.4

```r
install.packages(c(
  "evd", "copula", "ggplot2", "dplyr", "tidyr", "scales"
))
```

The following `.rds` files are required and produced by the thesis practical chapter:

| File | Contents |
|------|----------|
| `cop5d.rds` | Fitted 5D Gumbel copula object |
| `params_table.rds` | GEV parameter estimates per station (μ, σ, ξ) |
| `station_names.rds` | Station name vector |

Place these files in the same directory as the `.qmd` file, then:

```bash
quarto render dutch_cat_model.qmd
```

---

## Model Limitations

- **Hazard metric:** Daily precipitation totals are used as the intensity measure.
  Multi-day accumulation (3- or 5-day rolling maxima) would better capture riverine
  flooding; river discharge would be the most physically appropriate metric but
  sufficiently long public records are not available for Dutch gauging stations.
- **Copula:** The 5D Gumbel copula imposes a single dependence parameter across all
  pairs and is a simplification of the heterogeneous structure identified in the
  bivariate analysis.
- **Frequency:** Poisson rate λ = 1.5 is constant and independent of severity.
- **Vulnerability & Exposure:** All parameters and insured values are synthetic.
  This model is for educational and demonstration purposes only.

---

## Locations

| Location | Represented by |
|----------|---------------|
| Groningen | Groningen meteorological station |
| Roermond | Roermond meteorological station |
| Den Helder | Den Helder meteorological station |
| Hoofddorp | Hoofddorp meteorological station |
| Putten | Putten meteorological station |

---

> ⚠ Exposure values and vulnerability parameters are entirely synthetic.
> The hazard component is fitted to real observational data from NOAA NCEI GHCND.
> This model is for educational and demonstration purposes only.
