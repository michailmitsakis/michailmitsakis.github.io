---
layout: page
title: news-feed
description: A scheduled feed that collects, scores and summarises energy and maritime business news into a Notion database.
status: "tool in use"
result: "20+ sources into one Notion database"
importance: 8
img: assets/img/projects/diagram-news-feed.svg
category: consulting tools
permalink: /projects/news-feed/
---

{% include figure.liquid loading="eager" path="assets/img/projects/diagram-news-feed.svg" alt="Collect news, score it by keyword relevance, publish to a database" class="img-fluid rounded z-depth-1" %}

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" path="assets/img/projects/news-feed.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    The Notion database the feed populates: one entry per article, with source, date, relevance score and an automatic summary.
</div>

A tool built for my consulting work. It is an automated Python script that collects, prioritizes, and publishes energy & maritime news articles from multiple sources into a Notion database. Tracking renewable energy, hydrogen, maritime decarbonization, and related topics.

## How it works

- **Multi-Source Collection**: Monitors 20+ Greek and international news sources covering energy, maritime, and business topics
- **Smart Content Discovery**: Automatically discovers RSS feeds, news sitemaps, and scrapes websites when feeds aren't available
- **Intelligent Prioritization**: Scores articles based on keyword relevance (hydrogen, ammonia, carbon capture, wind energy, etc.)
- **Duplicate Prevention**: Tracks existing URLs to avoid republishing
- **Auto-Summarization**: Extracts and summarizes article content
- **Notion Integration**: Creates formatted database entries with title, URL, summary, published date, and source

**Stack:** Python · BeautifulSoup · cloudscraper · trafilatura· GitHub Actions

<!-- The repository is private. If you make it public, add `github: https://github.com/michailmitsakis/<repo>` to the front matter to show the GitHub icon on the project card. -->
