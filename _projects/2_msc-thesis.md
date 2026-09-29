---
layout: page
title: MSc Thesis
description: MSc thesis — Machine learning-driven optimization of electrodeposited Ni-W electrocatalysts for H2 evolution in alkaline electrolysis cells
importance: 2
permalink: /projects/msc-thesis/
---

<!-- TODO: fill in the bracketed placeholders, add a figure (e.g. polarisation curves or an SEM image), and link the thesis PDF. -->

**MSc Materials Science** · [DTU Energy] · [2023] · Supervisors: [Christodoulos Chatzichristodoulou]

Electrodeposition is a cheap, scalable way to make electrocatalysts. The catalyst you get, though, depends on a large set of coupled parameters: bath composition, current density or potential, pulse schedule, temperature, and deposition time. My thesis looked at how to choose those parameters to improve **hydrogen evolution reaction (HER)** performance. Further development continued in this repo **[Ax_bayes_opt_NiW](/projects/Ax_bayes_opt_NiW/)**.

## Approach

- Worked in wet-lab environment, using equipment related to: catalyst sample preparation (cutter, metallic press, sonication) and deposition bath mixing (chemical weighing and mixing, pH testing).
- Performed various electrochemical characterization measurements to determine electrode catalyst activity and stability (Chronopotentiometry (CP), Cyclic Voltammetry (CV)), as well as Impedance Spectroscopy (pEIS, gEIS) for accurate Electrochemically Active Surface Area (ECSA) calculations.
- Conducted data analysis and visualization.
- Applied and customized evolutionary machine learning algorithm [Dragonfly](https://link.springer.com/article/10.1007/s00521-015-1920-1) to significantly optimize the electrodeposition parameters for optimal catalyst activity and stability, with the goal of significantly reducing the number of manual lab experiments required.

## Highlights
- NiW beats bare Ni foam: NiW electrodes outperformed pure Ni foam for HER.
- Tungstate concentration matters: all top electrodes were deposited at 0.1 M tungstate, and they tended to activate rather than degrade during cycling.
- The gain was surface area, not better catalytic properties: intrinsic (ECSA-normalised) activity was mostly lower than Ni foam, so the improvement came mainly from the larger ECSA.
- Performance settles: the overpotential at 50 mA cm⁻² reaches a dynamic steady state after an initial activation or deactivation.
- Starting data matters: The initial cold-start for domain data points is critical but time-consuming to generate. A robust starting dataset is what avoids costly random exploration.
- Limitations and budgetary constraints: The process of glectrodepositing new electrodes, testing and characterizing them can take a full-day in the lab (> 9 hours) for 3 samples, restricting the usefulness of such approaches for exploring vast parameter spaces. That said, BO remains the most cost-effective optimization method for limited budgets. 
  
## What carried over

Most of my later work grew out of this thesis: I experienced first-hand how painstakingly accurate manual experiments need to be, and as such laborious and expensive, especially under strict budget and time limitations. Therefore, deciding where to query the parameter space next to minimzie the number of experiments and achieve the optimal set of parameters is a critical modelling problem. At the same time, the large variance in experimental setups and difficulty of achieving reproducibility in  results is a well-known issue in the field. these factors led me to exploring how materials informatics techniques can accelerate and even automate this process.