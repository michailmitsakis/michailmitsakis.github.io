---
layout: post
title: Your post title
date: 2026-10-01 09:00:00+0300
description: One sentence that appears under the title in the post list.
tags: electrochemistry hydrogen
categories: technical
giscus_comments: false
related_posts: true
toc:
  sidebar: left
---

Drafts in `_drafts/` are never published. To publish, move this file to `_posts/`
and rename it `YYYY-MM-DD-short-title.md` (the date prefix is required).

## A section

Markdown works as usual. Inline math uses `$$ ... $$`, e.g. $$\eta = E - E_{eq}$$.

Code blocks are highlighted:

```python
import numpy as np
```

Images go in `assets/img/` and are included with:

{% raw %}{% include figure.liquid path="assets/img/your-figure.png" class="img-fluid rounded z-depth-1" %}{% endraw %}
