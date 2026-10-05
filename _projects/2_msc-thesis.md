---
layout: page
title: MSc Thesis
description: Machine learning-driven optimization of electrodeposited Ni-W electrocatalysts for H2 evolution in alkaline electrolysis cells
img: assets/img/projects/thesis-loop.png
importance: 2
category: work
github: https://github.com/michailmitsakis/Ax_bayes_opt_NiW
permalink: /projects/msc-thesis/
---

<!-- TODO: link the thesis PDF and consider adding a lab figure (e.g. polarisation curves or an SEM image). -->

**MSc Engineering – Physics & Nanotechnology** · DTU Energy · 2023 · Supervisors: Christodoulos Chatzichristodoulou

**Code:** [github.com/michailmitsakis/Ax_bayes_opt_NiW](https://github.com/michailmitsakis/Ax_bayes_opt_NiW) · **Status:** thesis complete; Ax workflow working, next batch not yet measured

Electrodeposition is a cheap, scalable way to make electrocatalysts. The performance of the produced catalyst, however, depends on a large set of coupled
parameters: bath composition, current density, deposition time and pH. My thesis explored how to choose those parameters to improve **hydrogen evolution
reaction (HER)** performance, using multi-objective Bayesian optimization (BO) to trade off activity against stability. After graduating I rebuilt the same
loop in [Ax](https://ax.dev/), described [below](#rebuilding-the-workflow-in-ax).

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/thesis-loop.png" alt="Closed-loop Bayesian optimisation workflow" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    The closed loop used in the thesis: deposit a batch of Ni-W electrodes, measure activity and stability, refit one Gaussian process per objective, and let a multi-objective acquisition function choose the next recipes. About 30 electrodes were made over the thesis.
</div>

## Approach

- Worked in wet-lab environment, using equipment related to: catalyst sample preparation (cutter, metallic press, sonication) and deposition bath mixing
  (chemical weighing and mixing, pH testing).
- Performed various electrochemical characterization measurements to determine electrode catalyst activity and stability (Chronopotentiometry (CP), Cyclic
  Voltammetry (CV)), as well as Impedance Spectroscopy (pEIS, gEIS) for accurate Electrochemically Active Surface Area (ECSA) calculations.
- Conducted data analysis and visualization.
- Applied and customized the open source BO library [Dragonfly](https://github.com/dragonfly/dragonfly/) to optimize the electrodeposition parameters for
  optimal catalyst activity and stability, with the goal of significantly reducing the number of manual lab experiments required.
- Kept the search space to four parameters (tungstate concentration, current density, deposition time, pH). Temperature was dropped after it showed large
  systematic errors between measurements, which would only have confused the optimizer.

## Thesis findings

- **NiW beats bare Ni foam**: NiW electrodes outperformed pure Ni foam for HER.
- **Tungstate concentration matters**: all top electrodes were deposited at 0.1 M tungstate, and they tended to activate rather than degrade during cycling.
- **The gain was due to larger surface area, not better catalytic properties**: intrinsic (ECSA-normalised) activity was mostly lower than Ni foam, so the
  improvement came mainly from the larger ECSA.
- **Performance settles**: the overpotential at 50 mA cm⁻² reaches a dynamic steady state after an initial activation or deactivation.
- **Starting data matters**: the initial cold-start for domain data points is critical but time-consuming to generate. A robust starting dataset is what
  avoids costly random exploration.

## Rebuilding the workflow in Ax

After the thesis I rebuilt the loop as a small, tested Python package around Ax 1.3. Each round, Ax proposes a batch of three recipes; they are run in the
lab, the results go into a CSV, and the loop repeats. The CSV is the record of the campaign: the notebook rebuilds the Ax experiment from it every session,
and recipes without results stay pending so Ax does not propose them twice.

| Objective                                   | Direction                  | Threshold |
| ------------------------------------------- | -------------------------- | --------- |
| HER overpotential (mV, negative)            | maximize, i.e. closer to 0 | −350      |
| Overpotential slope (drift over CV cycling) | maximize; ≥ 0 means stable | −0.001    |

The search space is tungstate concentration (0.05–0.20 M), current density (5–125 mA/cm²), deposition time (60–600 s) and pH (5–10 in steps of 0.5).
Ax fits one Gaussian process per objective and picks each batch with qLogNEHVI, which favours recipes likely to push the Pareto front past the thresholds.

**About the data.** The analysis below uses 10 lab measurements as the initial design. Ax has since suggested two batches; those six recipes were completed
with illustrative values only to demonstrate the loop, and they appear only in the hypervolume plot, clearly marked.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" path="assets/img/projects/ax-pareto.png" alt="The 10 lab measurements with the measured and model-predicted Pareto fronts" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    The 10 lab measurements (blue), the two that form the measured Pareto front (circled), and the Pareto front the model predicts from them (purple, with 95% intervals on both objectives). Dashed lines are the objective thresholds.
</div>

### Results so far

- **Measured front.** Two of the 10 recipes are non-dominated: the best overpotential (−286 mV at 0.10 M tungstate, 50 mA/cm², 600 s, pH 7.5) and the best
  slope (+0.0017 at 30 mA/cm², pH 8.5, otherwise the same). Eight of the 10 meet both thresholds.
- **Predicted trade-off.** The model places the trade-off mainly along current density: about 30 mA/cm² for the best slope and about 48 mA/cm² for the best
  overpotential, with deposition times of 540–600 s and pH 8–9.5 throughout.
- **Next batch.** Ax proposes 34–46 mA/cm², pH 8–9.5 and ~600 s. Given the same data, [BayBE](https://emdgroup.github.io/baybe/stable/) proposes recipes in
  the same region (35–50 mA/cm², pH 8–9, 600 s), which is a useful independent check.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" path="assets/img/projects/ax-cross-validation.png" alt="Leave-one-out cross-validation for both objectives" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    Leave-one-out cross-validation: each recipe predicted by a model fitted to the other nine, with 95% predictive intervals. The overpotential model is usable (R² = 0.64); the slope model has not found a pattern yet (R² = 0.08), so the next suggestions are driven mainly by the overpotential.
</div>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" path="assets/img/projects/ax-model-insights.png" alt="Parameter importance and predicted overpotential over current density and pH" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    What the overpotential model has learned. Left: total-order Sobol indices; current density explains most of the predicted variation, then deposition time and pH. Right: predicted overpotential over current density and pH, other parameters held at the best measured recipe; crosses mark tested recipes.
</div>

The model gives tungstate concentration almost no weight. That sits awkwardly next to the thesis finding that the best electrodes used 0.1 M, but six of these
10 recipes used 0.1 M, so the data give the model little contrast to learn from. It is a question for the next batches rather than a conclusion.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" path="assets/img/projects/ax-hypervolume.png" alt="Hypervolume after each trial, with the illustrative trials shaded" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    Hypervolume (the area the measured Pareto front dominates, relative to the thresholds) after each trial. Trials 0–9 are lab measurements; trials 10–15 (shaded, dashed) are recipes Ax actually suggested, completed with illustrative values to show what a few rounds look like.
</div>

**Stack:** Ax 1.3 · BoTorch · BayBE · Dragonfly (thesis) · pandas · matplotlib · pytest

## Limitations

- **No Ax suggestion has been measured yet.** There is no evidence so far that the optimizer beats the hand-picked initial design; the illustrative rows
  only demonstrate the mechanics.
- **Ten points in four dimensions.** Every model-based conclusion above is tentative, including the predicted front and the parameter importances.
- **The stability metric needs rethinking.** The slope model has essentially no predictive power (R² = 0.08). A better candidate, noted in my thesis
  conclusion, is the slope of the overpotential over only the last few CV cycles, after the initial activation or deactivation has settled.
- **Noise is inferred, not measured.** Without replicates, the model estimates one noise level per objective. Replicates could be passed to Ax as
  mean ± standard error.
- **The search space may be too narrow.** Every proposal sits at the 600 s upper limit for deposition time, which suggests extending that range if the
  process allows.
- **Lab throughput limits the whole approach.** Depositing, testing and characterizing three electrodes can take a full day in the lab (over 9 hours), so
  BO here is about spending a small budget well, not exploring a vast space. Within that constraint it remains the most cost-effective option.

The full list, including Ax version pinning and the BayBE comparison setup, is in the
[README](https://github.com/michailmitsakis/Ax_bayes_opt_NiW#limitations).

## What carried over

Most of my later work grew out of this thesis: I experienced first-hand how painstakingly accurate manual experiments need to be, and as such laborious and
expensive, especially under strict budget and time limitations. Therefore, deciding where to query the parameter space next to minimize the number of
experiments and achieve the optimal set of parameters is a critical problem. At the same time, the large variance in experimental setups and difficulty of
achieving reproducibility is a well-known in the field. These factors led me to exploring how materials informatics techniques can accelerate and even
automate this process.
