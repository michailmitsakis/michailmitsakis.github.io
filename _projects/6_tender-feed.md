---
layout: page
title: tender-feed
description: A scheduled monitor for public tenders and funding calls, with keyword prioritisation and weekly email digests.
importance: 6
category: consulting tools
permalink: /projects/tender-feed/
---

A tool built for my consulting work. It watches the announcement pages of public tenders and funding calls, including energy utilities, development banks, EU agencies, and several Interreg programmes. Once a week it emails a digest of new items.

## How it works

- **Configurable sources.** Each source is defined in a YAML file and scraped with Scrapling, which handles static, dynamic, and protected pages. A generic extractor covers most sites, with site-specific extractors where it doesn't.
- **Only new items.** Each source keeps its own record of items already seen, so the digest lists only what's new.
- **Prioritisation.** Items are ranked by energy and decarbonisation keywords in English and Greek, and by European geography.
- **Scheduling.** GitHub Actions runs the monitor weekly and sends the email digest.

**Stack:** Python · Scrapling · GitHub Actions

<!-- The repository is private. If you make it public, add `github: https://github.com/michailmitsakis/<repo>` to the front matter to show the GitHub icon on the project card. -->
