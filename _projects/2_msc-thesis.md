---
layout: page
title: MSc Thesis
description: Machine learning-driven optimization of electrodeposited Ni-W electrocatalysts for H2 evolution in alkaline electrolysis cells
img: assets/img/projects/thesis-loop.png
importance: 2
category: work
permalink: /projects/msc-thesis/
---

<!-- TODO: fill in the bracketed placeholders, add a figure (e.g. polarisation curves or an SEM image), and link the thesis PDF. -->

**MSc Engineering – Physics & Nanotechnology** · DTU Energy · 2023 · Supervisors: Christodoulos Chatzichristodoulou

Electrodeposition is a cheap, scalable way to make electrocatalysts. The performance of the produced catalyst, however, depends on a large set of coupled parameters: bath composition, current density or potential, deposition time and pH. My thesis explored how to choose those parameters to improve **hydrogen evolution reaction (HER)** performance. Further development continued in this repo **[Ax_bayes_opt_NiW](https://github.com/michailmitsakis/Ax_bayes_opt_NiW)**.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/thesis-loop.png" alt="Closed-loop Bayesian optimisation workflow" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    The closed loop used in the thesis: deposit a batch of Ni-W electrodes, measure activity and stability, refit one Gaussian process per objective, and let a multi-objective acquisition function choose the next recipes. About 30 electrodes were made over the thesis.
</div>

## Approach

- Worked in wet-lab environment, using equipment related to: catalyst sample preparation (cutter, metallic press, sonication) and deposition bath mixing (chemical weighing and mixing, pH testing).
- Performed various electrochemical characterization measurements to determine electrode catalyst activity and stability (Chronopotentiometry (CP), Cyclic Voltammetry (CV)), as well as Impedance Spectroscopy (pEIS, gEIS) for accurate Electrochemically Active Surface Area (ECSA) calculations.
- Conducted data analysis and visualization.
- Applied and customized the open source BO library [Dragonfly](https://github.com/dragonfly/dragonfly/) to optimize the electrodeposition parameters for optimal catalyst activity and stability, with the goal of significantly reducing the number of manual lab experiments required.

## Highlights

- **NiW beats bare Ni foam**: NiW electrodes outperformed pure Ni foam for HER.
- **Tungstate concentration matters**: all top electrodes were deposited at 0.1 M tungstate, and they tended to activate rather than degrade during cycling.
- **The gain was due to larger surface area, not better catalytic properties**: intrinsic (ECSA-normalised) activity was mostly lower than Ni foam, so the improvement came mainly from the larger ECSA.
- **Performance settles**: the overpotential at 50 mA cm⁻² reaches a dynamic steady state after an initial activation or deactivation.
- **Starting data matters**: The initial cold-start for domain data points is critical but time-consuming to generate. A robust starting dataset is what avoids costly random exploration.
- **Limitations and budgetary constraints**: The process of electrodepositing new electrodes, testing and characterizing them can take a full-day in the lab (> 9 hours) for 3 samples, restricting the usefulness of such approaches for exploring vast parameter spaces. That said, BO remains the most cost-effective optimization method for limited budgets.

## Rebuilding the workflow in Ax

After the thesis I rebuilt the same loop in [Ax](https://ax.dev/), with qNEHVI proposing batches of three recipes. The plots below come from a demonstration run with placeholder values, not thesis measurements. They show what the workflow tracks.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" path="assets/img/projects/ax-pareto.png" alt="Observed results by batch with Pareto front" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    Demonstration run: each result plotted by activity (overpotential) and stability (drift slope under a stress test), coloured by batch. Circled points are non-dominated, and the shaded region inside the −350 mV / −0.001 mV s⁻¹ thresholds is the hypervolume. Placeholder values.
</div>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" path="assets/img/projects/ax-hypervolume.png" alt="Hypervolume across the run" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    Hypervolume after each experiment in the same demonstration run. Both BO batches extend the front beyond the best initial result. Placeholder values.
</div>

## What carried over

Most of my later work grew out of this thesis: I experienced first-hand how painstakingly accurate manual experiments need to be, and as such laborious and expensive, especially under strict budget and time limitations. Therefore, deciding where to query the parameter space next to minimize the number of experiments and achieve the optimal set of parameters is a critical problem. At the same time, the large variance in experimental setups and difficulty of achieving reproducibility is a well-known in the field. These factors led me to exploring how materials informatics techniques can accelerate and even automate this process.
