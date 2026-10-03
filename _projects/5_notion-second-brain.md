---
layout: page
title: notion-second-brain
description: A fully local RAG agent over a Notion workspace — hybrid retrieval, reranking, persistent memory, and an evaluation harness.
img: assets/img/projects/notion-second-brain.png
importance: 5
category: work
github: https://github.com/michailmitsakis/notion-second-brain
permalink: /projects/notion-second-brain/
---

**Code:** [github.com/michailmitsakis/notion-second-brain](https://github.com/michailmitsakis/notion-second-brain)

I keep my notes and research in Notion. I wanted to summarize and query them without sending them to a cloud API, so I built a complete retrieval-augmented generation stack that runs entirely on one machine.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/notion-second-brain.png" title="Streamlit interface" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    The Streamlit interface, with memory enabled. A command-line interface (without memory) is also available.
</div>

## What's in it

- **Hybrid retrieval.** Dense embeddings and sparse BM25 are combined with reciprocal-rank fusion, and results are then reranked by a cross-encoder.
- **Ingestion.** Notion exports are converted to markdown, and PDFs and images go through OCR. Chunking is sentence-aware and keeps code blocks and tables intact.
- **Memory.** File-based per-session and long-term memory is injected into the agent at runtime.
- **Evaluation.** An evaluation harness scores answers against a four-criterion anchored rubric.
- **Observability.** Tracing through Arize Phoenix is optional.

**Stack:** pydantic-ai · Ollama · Qdrant · marker · Streamlit · Docker. It is tuned for a single 12 GB-VRAM machine.

The same concerns drive my materials work: retrieval that can be checked, evaluation you can trust, and nothing that depends on a service you don't control.
