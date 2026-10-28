---
layout: project
title: CaMEL-RAG
description: A retrieval-augmented LLM that answers natural-language questions about catalyst adsorption energies, grounded in Open Catalyst data.
status: "hackathon · 2025"
result: "R² = 1.00 vs DFT on 500 in-index queries"
img: assets/img/projects/diagram-camel-rag.svg
importance: 6
category: work
github: https://github.com/michailmitsakis/CaMEL-RAG
permalink: /projects/camel-rag/
links:
  - { label: Paper, url: "https://arxiv.org/abs/2605.03205" }
event: 2025 LLM Hackathon for Applications in Materials Science & Chemistry
stack: [Python, FAISS, sentence-transformers, OpenAI API, pandas, Jupyter]
stands: Hackathon prototype. Grounding works on in-index queries; held-out and out-of-distribution queries are untested.
team: Code4Catalysis-KFUPM (Montassar Bouzidi, Nur Allif Fathurrahman, A. B. M. Ashikur Rahman, Tasnim Ahmed, Michail Mitsakis)
---

{% include figure.liquid loading="eager" path="assets/img/projects/diagram-camel-rag.svg" alt="Retrieve Open Catalyst records, answer with citations, check against DFT" class="img-fluid rounded z-depth-1" %}

## Asking a DFT database a question
{: data-kicker="the problem"}

Catalyst-screening datasets such as Open Catalyst hold millions of DFT results, but a researcher can't simply ask them a question. Our idea for the hackathon was a natural-language front end: ask about an adsorbate on a surface and get back a number traceable to the underlying calculation, rather than a fluent guess.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/camel-rag-workflow.jpg" title="CaMEL-RAG workflow" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    Workflow from the hackathon submission.
</div>

## How it works
{: data-kicker="the approach"}

- **Knowledge base:** records from an Open Catalyst-derived table (adsorption energies, descriptors and structure metadata) are embedded with `all-MiniLM-L6-v2` sentence-transformer embeddings and indexed in FAISS.
- **Retrieval:** each question retrieves its top-_k_ records. They are inserted into the prompt with `[#doc_id]` tags, so every answer cites the records it came from.
- **Generation:** an LLM (GPT-4.1-mini by default; the model is swappable) answers using only the retrieved context.

<div class="row justify-content-sm-center">
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/camel-rag-parity.png" title="CaMEL-RAG vs DFT adsorption energies" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    500 test queries: the adsorption energies returned by CaMEL-RAG against the DFT values in the index.
</div>

## Results
{: data-kicker="what we found"}

On 500 test queries, the returned adsorption energies matched the DFT values exactly (R² = 1.00). These queries ask about records that are **in the index**, so the test shows that retrieval and grounding work: the model reports the stored number instead of making one up. It does **not** show that the model can predict energies for systems it hasn't seen. The obvious next step is an evaluation on held-out and near-duplicate queries, and detecting out-of-distribution requests.

The hackathon's outcomes, including this project, are summarized in the [event paper on arXiv](https://arxiv.org/abs/2605.03205).

