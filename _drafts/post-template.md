---
layout: post
title: Your post title
date: 2026-10-01 09:00:00+0300
description: One sentence that appears under the title in the post list.
tags: electrochemistry hydrogen
categories: technical
level: intermediate # introductory, intermediate or advanced; shown after the reading time
# series: thesis notes # optional: a label above the title for posts that belong together
# part: 1
takeaways: # the "what you'll take away" box at the top; 2–4 lines, *markdown* works
  - The one thing a reader should remember.
  - The second thing, with the number that supports it.
  - When this does not apply.
giscus_comments: false
related_posts: true
toc:
  sidebar: left
# mermaid:
#   enabled: true # turn on for flowcharts written in ```mermaid blocks
---

Drafts in `_drafts/` are never published. To publish, move this file to `_posts/`
and rename it `YYYY-MM-DD-short-title.md` (the date prefix is required).

House rules: every number or claim from someone else gets a link or a reference;
every number from my own work links to the notebook or repo that produced it.

## The problem

What question does this post answer, and why does it matter? One or two paragraphs.

## What I did

The method, with the code or setup someone would need to repeat it.

Code blocks are highlighted:

```python
import numpy as np
```

Images go in `assets/img/` and are included with:

{% raw %}{% include figure.liquid path="assets/img/your-figure.png" class="img-fluid rounded z-depth-1" zoomable=true %}{% endraw %}

## What I found

The results, with numbers. Inline math uses `$$ ... $$`, e.g. $$\eta = E - E_{eq}$$.

> A tip the reader should not miss.
{: .block-tip }

## When it doesn't apply

Limitations, and the cases where a different approach is better.

> A pitfall or a common mistake.
{: .block-warning }

## Try it yourself

A short exercise, or a link to the notebook with the data.

## References

1. Author, *Title*, Journal **volume**, page (year). [doi](https://doi.org/...)
