---
layout: page
title: BayBE one more time
description: Hackathon project — Bayesian optimisation with BayBE to screen small-molecule corrosion inhibitors for aluminium alloys, including transfer learning between alloys.
img: assets/img/projects/baybe-featurization-aa2024.png
importance: 3
category: work
github: https://github.com/michailmitsakis/baybe_project-surface-science-syndicate
permalink: /projects/baybe-corrosion-inhibitors/
---

**Code:** [GitHub](https://github.com/michailmitsakis/baybe_project-surface-science-syndicate) · **Video:** [YouTube](https://youtu.be/kIRxGdwmLSY) · **Event:** [Bayesian Optimization Hackathon for Chemistry and Materials](https://doi.org/10.26434/chemrxiv-2025-dzh5z) (Acceleration Consortium × Merck KGaA, March 2024) · **Team:** Surface Science Syndicate

Finding a good corrosion inhibitor means testing many candidate molecules across many conditions, and each test is a real electrochemical experiment. We asked how much of that screening Bayesian optimisation can save. We used [BayBE](https://github.com/emdgroup/baybe), Merck's open-source Bayesian optimisation library, on a published database of electrochemical responses of small organic molecules on aluminium and five aluminium alloys (AA1000, AA2024, AA5000, AA6000, AA7075).

## Setup

- **Search space:** the inhibitor molecule (as SMILES), exposure time, pH, inhibitor concentration and salt concentration, with inhibition efficiency as the target to maximize.
- **Molecular encodings compared:** one-hot, Mordred descriptors, RDKit descriptors and Morgan fingerprints, each against a random-sampling baseline. This tests whether telling the optimizer something about chemistry actually helps.
- **Protocol:** simulated campaigns against the measured data, with 50 experiments each, one experiment per round, and 10 Monte Carlo repetitions.
- **Transfer learning:** a campaign on AA2024 seeded with prior data from AA1000, using BayBE's task parameters, compared with a campaign starting from scratch.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/baybe-featurization-aa2024.png" title="Cumulative best efficiency by encoding, AA2024" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/baybe-transfer-aa1000-aa2024.png" title="Transfer learning from AA1000 to AA2024" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    Left: best inhibition efficiency found so far on AA2024 for each molecular encoding and for random sampling. Right: an AA2024 campaign seeded with AA1000 data ("Transfer") against one starting from scratch ("Fresh").
</div>

## What we found

- On AA2024, every strategy reached near-maximal efficiency within roughly 20 experiments, **including random sampling**. That says as much about the dataset, which contains many strong inhibitors, as about the optimizer. Morgan fingerprints were the slowest to get going.
- **Transfer learning paid off.** Seeding the AA2024 campaign with AA1000 data reached about 96% efficiency by the 12th experiment. The fresh campaign was still at about 89% after 25.
- The main lesson: benchmark against random sampling, and pick datasets where the optimum is actually hard to find. Otherwise an easy benchmark can make any optimizer look good.

The hackathon's outcomes, including this project, are summarized in the [event paper on ChemRxiv](https://doi.org/10.26434/chemrxiv-2025-dzh5z).

**Stack:** BayBE · RDKit · Mordred · pandas · Jupyter

**Data:** T. L. P. Galvão _et al._, [CORDATA](https://doi.org/10.1038/s41529-022-00259-9), _npj Mater. Degrad._ **6**, 48 (2022); C. Özkan _et al._, [npj Mater. Degrad. **8**, 21 (2024)](https://doi.org/10.1038/s41529-024-00435-z).
