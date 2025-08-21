---
title: "Validating Allometric Equations: Model Selection, Bias Correction, and Performance Optimization"
date: 2024-11-04
author: 
  - name: Murphy, S
    orcid: 0000-0002-1792-0351 
    email: seamusrobertmurphy@gmail.com
abstract: > 
  This workflow demonstrates use of Monte Carlo tools in uncertainty quantification for purpose of allometric biomass model selection and systematic bias minimization in compliance with Section 8 of the ART-TREES carbon registry standards.
keywords:
  - REDD+
  - Allometric Models
  - Hyperparameter Tuning
  - Carbon verification
format: 
  html:
    toc: true
    toc-location: right
    toc-title: "**Contents**"
    toc-depth: 5
    toc-expand: 4
    theme: [minimal, styles.scss]
highlight-style: github
df-print: kable
keep-md: true
prefer-html: true
bibliography: references.bib
engine: knitr
---

```{=html}
<style type="text/css">
div.column {
    display: inline-block;
    vertical-align: top;
    width: 50%;
}

#TOC::before {
  content: "";
  display: block;
  height:200px;
  width: 200px;
  background-size: contain;
  background-position: 50% 50%;
  padding-top: 80px !important;
  background-repeat: no-repeat;
}
</style>
```

### Overview

The following workflow demonstrates statistical validation and calibration procedures for allometric biomass models that meet ART-TREES requirements for carbon registry emissions reporting. This was derived in response to gaps in model selection methodology, specifically aimed at avoiding Types 1-4 statistical errors and over-fitting. Carbon registries mandate implementation of robust model selection protocols that ensure spatial and species relevance while recommending the optimization of predictive accuracy through systematic bias correction and hyper parameter tuning.

Eleven candidate allometric equations were evaluated using required uncertainty metrics including: 1) 90% confidence interval estimation, goodness-of-fit statistics (R²-adj, RMSE, AIC), residual analysis, and distribution testing across four species (*Betula pendula, Betula pubescens, Picea sitchensis, Sorbus aucuparia*). The workflow also documents examples of Monte Carlo error propagation and model selection following CEOS/NASA protocols for RMSE minimization and coefficient of variation analysis.

Among the eleven regression models tested, eight achieved acceptable performance statistics for carbon project applications. Evaluation of RMSE performance identified optimal equations in models `M3` for *Betula pendula* (RMSE: 2.38), `M7` for B. *pubescens* (RMSE: 0.00), `M9` for P. *sitchensis* (RMSE: 1.47), and `M11` for *S. aucuparia*. Polynomial transformation testing revealed significant improvements in model fit while avoiding overfitting through systematic validation protocols. Essentially, Monte Carlo simulations confirmed systematic bias \<5% across species, meeting ART-TREES requirements for uncertainty deduction calculations.

This framework provides carbon project developers with statistically robust model selection and calibration procedures required for registry compliance, reducing estimation uncertainty and audit risk while ensuring biomass predictions meet statistical performance standards mandated by Verra, ART-TREES, and ACR protocols.

### Introduction

IPCC Directives: Quantifying uncertainty in biomass estimates is a core directive of the IPCC (Eggleston et al., 2006) and, following UNFCCC Decision 4/CP.15 (2010), applies mandatorily to all REDD+ mechanism implementations. This regulatory foundation establishes uncertainty assessment not as optional best practice, but as legally required compliance for all forest carbon projects under international climate agreements.

Failing to integrate the whole chain of error when estimating per-hectare biomass from inventory data and allometric equations artificially reduces confidence interval width (Fortin & DeBlois, 2010). This principle directly addresses the systematic underestimation of uncertainty that occurs when:

-   Allometric model selection uncertainty is ignored or inadequately assessed
-   Parameter estimation errors are not propagated through calculations
-   Measurement uncertainties are treated independently rather than systematically combined
-   Model structural uncertainty is omitted from confidence interval calculations

The choice of allometric model represents a major component of total error and reaches maximum impact when all candidate models are treated as equally likely without systematic validation protocols.

ART-TREES Section 8 provides the operational framework for implementing IPCC/UNFCCC uncertainty mandates, requiring that emission reductions be adjusted based on estimated uncertainty to prevent systematic over-crediting that undermines international climate accounting integrity.

### Allometry Validation Requirements

ART-TREES Section 8 mandates that emission reductions be adjusted based on estimated uncertainty to minimize over-crediting risk. Key requirements include:

-   90% Confidence Interval Estimation: Half-width expressed as percentage of estimated emissions
-   Monte Carlo Error Propagation: Minimum 10,000 simulation iterations using Approach 2 methodology
-   Whole-Chain Error Integration: Systematic combination of allometric, measurement, and sampling uncertainties
-   Systematic Bias Minimization: Validation against independent destructive harvest datasets
-   Uncertainty Deduction Formula: As cited on page 46 of the TREES Standards V2.0, calculations of uncertainty deductions are derived using the following formula:

$$
UNC_t = (GHG ER_t + GHG REMV_t) \times UA_t \text{.                      EQ 10}
$$

|  |  |
|----|----|
| $UNC_t$ | Uncertainty deduction for year $t$ ($tCO_2e$) |
| $GHG ER_t$ | Gross greenhouse gas emissions reductions for year $t$ ($tCO_2e$) |
| $GHG REMV_t$ | Gross greenhouse gas removals for year $t$ ($tCO_2e$) |
| $UA_t$ | The uncertainty adjustment factor for year $t$ |

### Common Allometric Errors

Allometric Measurement Errors:

-   Load Cell Precision: ±0.1-0.5 kg field measurement accuracy requirements
-   Moisture Content (MC) Uncertainty: CV \>15% across sites, requiring stratified sampling
-   Wood Density Variability: Sampling error dominates instrument precision errors
-   Volume Estimation Error: Conical approximation vs TLS-based measurements

Allometric Modelling Errors:

-   Calibration Sample Size: Minimum 100 trees required for robust parameter estimation
-   Validation Requirements: Independent sample ≥50 trees per species [@paul2018validation]
-   Range of Validity: Model extrapolation beyond calibration DBH range introduces bias
-   Form Factor Uncertainty: Theoretical $f×ρ×H×D²$ relationship vs empirical functions

2006 IPCC Guidelines Section 2.3.1 New guidance: Authors should refer to the Annotated List of Issues. In addition, authors should consider approaches based on individual tree and stand level allometric equations, including considering validation.

:::: cell
::: cell-output-display
```{=html}
<table class="table table-striped table-hover" style="margin-left: auto; margin-right: auto;">
<caption>Carbon Registry Uncertainty Requirements vs CEOS/NASA Protocols</caption>
 <thead>
  <tr>
   <th style="text-align:left;"> Component </th>
   <th style="text-align:left;"> ART_TREES_Standard </th>
   <th style="text-align:left;"> CEOS_NASA_Protocol </th>
   <th style="text-align:left;"> Implementation </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Confidence Interval </td>
   <td style="text-align:left;"> 90% CI half-width </td>
   <td style="text-align:left;"> Independent validation </td>
   <td style="text-align:left;"> Prediction intervals </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Bias Assessment </td>
   <td style="text-align:left;"> Systematic bias &lt;5% </td>
   <td style="text-align:left;"> Equivalence testing </td>
   <td style="text-align:left;"> Cross-validation </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Sample Size </td>
   <td style="text-align:left;"> ≥50 trees validation </td>
   <td style="text-align:left;"> ≥100 trees calibration </td>
   <td style="text-align:left;"> Destructive harvest </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Error Propagation </td>
   <td style="text-align:left;"> Monte Carlo n=10,000 </td>
   <td style="text-align:left;"> Bootstrapping allowed </td>
   <td style="text-align:left;"> R simulation </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Precision Requirements </td>
   <td style="text-align:left;"> Load cell ±0.5kg max </td>
   <td style="text-align:left;"> MC stratified by PFT </td>
   <td style="text-align:left;"> Field protocol training </td>
  </tr>
</tbody>
</table>
```
:::
::::
