---
layout: page
title: catalyst-kg-agent
description: Knowledge-graph-grounded, cost-aware multi-agent system for choosing the cheapest sufficient next step in a materials-discovery campaign.
img: assets/img/projects/catalyst-kg-agent.png
importance: 1
github: https://github.com/michailmitsakis/catalyst-kg-agent
permalink: /projects/catalyst-kg-agent/
---

**Code:** [github.com/michailmitsakis/catalyst-kg-agent](https://github.com/michailmitsakis/catalyst-kg-agent) · **Status:** working demo

Self-driving labs face the same decision at every step: given a target property and a limited budget, should the next move be a cheap database lookup, a fast machine-learned surrogate estimate, or an expensive high-fidelity experiment? Get it wrong and the budget goes on redundant expensive steps, or an unreliable surrogate result gets trusted without a check.

This project is a small version of that decision system that runs on a local machine. Its three parts are:

- a **knowledge graph** built from Materials Project data, which grounds the agents and serves as their memory
- a **machine-learned interatomic potential (MACE)** used as the property surrogate
- a **role-specialised multi-agent system** that chooses actions under an explicit cost budget

The graph covers 189 materials across 27 chemical systems. These include HER-relevant transition-metal phosphides, sulfides, and carbides, and OER-relevant oxides, with Pt and Ir–O as benchmarks.

## How it works

A **Planner** (an LLM) orders the candidates. A **Retriever** queries the knowledge graph, and a **Predictor** scores candidates with MACE. Before any escalation, a **Critic** has to approve it using two independent checks:

1. **Thermodynamic stability** — the energy above the convex hull, taken from Materials Project data rather than from the surrogate.
2. **Surrogate trustworthiness** — the maximum residual force MACE predicts on a DFT-relaxed structure. DFT's own forces on those structures are close to zero by construction. A large MACE residual therefore means the surrogate is outside the region where it can be trusted.

A **Scribe** writes predictions back into the graph, so later campaigns start from accumulated results instead of from scratch. All messages between agents use strict Pydantic schemas.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/catalyst-kg-agent.png" title="MACE vs CGCNN parity plots" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    MACE (zero-shot) vs a CGCNN baseline trained on the corpus. MACE's error is bimodal: non-oxides track DFT closely, while oxides are offset by about 1 eV/atom.
</div>

## Selected results

- **Surrogate accuracy.** Zero-shot MACE reaches a formation-energy MAE of **0.114 eV/atom on non-oxides**. On oxides its error is systematically offset by about 1 eV/atom. This points to an energy-correction mismatch in the reference data rather than a uniform inaccuracy, and the README traces it.
- **Honest baselines.** The CGCNN baseline is evaluated with **composition-disjoint cross-validation** as well as a random split. 69% of materials share a formula with another entry, so a random split leaks polymorphs of the same composition across folds.
- **The graph accumulates across campaigns.** Over three budgeted campaigns, later runs skipped 19 and then 36 materials that were already scored. A targeted query (_"Find stable Ni–P HER catalysts"_) finished with more than three-quarters of its budget unspent and selected **Ni₂P**.

**Stack:** PyTorch Geometric · MACE · BoTorch/Ax · pydantic-ai · Ollama · NetworkX · MLflow

**Limitations**, stated plainly: the "expensive experiment" step is simulated. The pipeline selects for stability and surrogate confidence, not catalytic activity, and it predicts no adsorption energies or overpotentials. The full discussion is in the [README](https://github.com/michailmitsakis/catalyst-kg-agent#limitations).
