---
layout: project
title: SALEM
description: Self-improving agentic loops that score LLM-extracted literature features against a measured property, here the PL peak of hybrid perovskites.
status: "OSI hackathon · 2026"
result: "one question: error 98.5 → 86.9 nm"
img: assets/img/projects/diagram-salem.svg
importance: 4
category: work
github: https://github.com/zakidotai/kg_osi_26
permalink: /projects/salem/
event: Open Scientific Intelligence (OSI) Hackathon · October 2026
stack: [MatSciBERT, GPyTorch, BM25 + dense hybrid search]
stands: Two-day hackathon prototype. The scoring and pruning loop works; accepting a change needs paired, multi-seed cross-validation to beat the noise.
team: Sven Voigt, Defne Circi, Xiaoquan Liu, Mohd Zaki, Zhanzhao Li, Eltjo Mante, Michalis Mitsakis
---

{% include figure.liquid loading="eager" path="assets/img/projects/diagram-salem.svg" alt="Extract questions, score them with a GP, prune and rewrite" class="img-fluid rounded z-depth-1" %}

**SALEM** (**S**elf-improving **A**gentic **L**oops: LLM-**E**xtracted features for **M**aterials Property Prediction) gives literature-mining AI agents a way to check their own work. A Gaussian process (GP) predicts a trusted measured property, here the photoluminescence (PL) peak of hybrid perovskites, from what the agent extracts. The agent then keeps rewriting its questions and database queries to lower that prediction error, in a self-improving loop.

## Extraction without ground truth
{: data-kicker="the problem"}

Knowledge graphs built by AI agents rarely come with ground truth, so nothing tells the agent whether its extractions are any good, and it has nothing to improve against. SALEM borrows one trusted value per record, the curated PL peak, and turns the GP's held-out error into a score the agent can minimize.

## How it works
{: data-kicker="the approach"}

- **Hybrid search:** HybriD³ records (formula, synthesis and experimental text, PL peak) are indexed, and a combined dense-embedding and BM25 ranking retrieves the relevant ones.
- **Agentic questions:** an agent breaks a synthesis down into targeted questions: which metal, which solute, what temperature, how long, and so on.
- **Question-by-question scoring:** an LLM answers each question for every record, and each embedded answer becomes a feature.
- **Gaussian process:** a GP predicts the PL peak from these features plus the formula, the measurement conditions and MatSciBERT embeddings. Its cross-validated error is the score.
- **Agentic loop:** switching questions on and off gives error gradients. The agent drops, rewrites and adds questions, keeps only the changes that beat the noise, and chooses database queries the same way.

## What we found
{: data-kicker="what we found"}

- **Fewer, sharper questions win.** The metal question alone cut the error from 98.5 to 86.9 nm. Adding the other nine one by one ended at 91.7 nm, worse than metal alone. So the loop has to prune questions, not just accumulate them.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/salem-questions.png" alt="GP error as extracted questions are added one at a time" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    Cross-validated GP error on the PL peak as questions are added one at a time, in order of how well each does alone. Blue: question features only. Orange: with the formula and PL measurement conditions. Lines are the mean over three CV seeds, bands the min–max. The dashed line is the error of always predicting the mean.
</div>

- **Features interact.** Formula + metal (78.2 nm) and formula + sample form (78.5 nm) are the best pairs. Formula + halide is redundant, which makes sense since the formula already contains the halide. Excitation + solute conflict: together they do 8.8 nm worse than the better of the two alone.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" path="assets/img/projects/salem-pairs.png" alt="GP error for each pair of features, and pair error relative to the better single feature" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    Left: GP error for each pair of features, with each feature alone on the diagonal. Right: pair error minus the better of its two single features. Blue pairs complement each other; red pairs are redundant or conflict.
</div>

- **Queries specialize.** Focused subsets beat their own mean baseline by far more than the full dataset does: by 52% for slow-cooled crystals, 38% for laser-excited measurements and 30% for spin-coated films, against 17% for all 209 records. Not every subset gains, though: lead iodides improved by only 3%.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" path="assets/img/projects/salem-subsets.png" alt="GP error per data subset against the mean baseline, with the most useful features" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    Left: GP error with all features (blue) against always predicting the subset's mean (grey), for all records and for focused subsets. Right: the two features that do best on their own, and the two whose removal hurts most (leave-one-feature-out), with the change in error in nm.
</div>


- **Retrieval features carried the most weight.** On the full dataset, se_avg was the best single feature (−16.8 nm on its own) and se_max the one the model could least do without (removing it costs 6.2 nm).

- **Noise sets the bar.** A single cross-validation split can move the score by up to 15 nm, and changes under about 3 nm are noise. Accepting a change therefore needs paired, multi-seed cross-validation.

**The main lesson**: extraction quality can be scored without hand-labelled ground truth, as long as there is one trusted measured property to predict. The score is noisy, though, so each change has to beat seed-to-seed variation before the agent keeps it.

**Data:** [HybriD³ materials database](https://hybrid3.duke.edu/research/materials-database), Duke University.
