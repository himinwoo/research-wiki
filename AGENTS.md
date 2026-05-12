# LLM Wiki — Autonomous Driving Sensor Calibration & SLAM

A personal knowledge base of autonomous driving multi-sensor (camera, LiDAR, radar) calibration and SLAM papers, following [Karpathy's LLM Wiki pattern](https://gist.github.com/karpathy/1dd0294ef9567971c1e4348a90d69285):

```
Original PDF → sources/*.md (LLM summary) → wiki/{category}/*.md (final page)
```

**Language policy**: All wiki content is in English. Conversation can be in any language.

---

## THE FOUR RULES

These rules are the core of the system. They prevent hallucination and keep every claim traceable.

1. **No web search.** Never use `WebSearch` or `WebFetch` to fill gaps. The point of this wiki is that every answer is grounded in papers we actually have.
2. **Answer from the wiki first.** Use `sources/` and `wiki/` as the only sources of truth.
3. **If the wiki is insufficient, re-read the PDF.** Go to `papers/{author}-{year}-{words}.pdf` and extract more detail. Then update the wiki.
4. **If the wiki has no paper on the topic, say so.** Tell the user *"I don't have a paper on this — please give me the PDF."* Do not improvise.

These rules apply to **every** response, including overview pages: cite only papers that exist in the wiki.

---

## Repository Structure

```
research-wiki/
├── AGENTS.md               # Project operating charter (this file)
├── papers/                 # Original PDFs (cp, never symlink)
│   └── {author}-{year}-{title-5-words}.pdf
├── sources/                # PDF summaries (English)
│   └── {author}-{year}-{title-5-words}.md
└── wiki/                   # Wiki pages (English)
    ├── index.md            # Full page catalog (category + key papers)
    ├── log.md              # Work log
    ├── camera-calib/
    ├── lidar-calib/
    ├── radar-calib/
    ├── multi-sensor/
    ├── slam/
    ├── targetless-calib/
    ├── online-calib/
    ├── concepts/
    └── overviews/          # Synthesis pages (where compounding happens)
```

## File Naming Convention

All three tiers (PDF, source, wiki) share the same stem:

```
{first-author-lastname}-{year}-{first-5-title-words}.{ext}
```

- Lowercase, special chars stripped, spaces → `-`
- Year is 4 digits
- Consortium papers: use consortium name (e.g. `1000-genomes-project-2015-...`)

## Categories

| Category | Includes |
|---|---|
| `camera-calib` | Camera intrinsic/extrinsic calibration, checkerboard, lens distortion |
| `lidar-calib` | LiDAR-camera, LiDAR-LiDAR extrinsic calibration |
| `radar-calib` | Radar-camera, radar-LiDAR extrinsic calibration |
| `multi-sensor` | Multi-sensor fusion, timestamp synchronization, sensor setup |
| `slam` | LiDAR SLAM, visual SLAM, radar SLAM |
| `targetless-calib` | Target-free calibration, motion-based, scene-based methods |
| `online-calib` | Real-time auto-calibration, online calibration systems |
| `concepts` | Key methods, algorithms, and mathematical concepts explained generically |
| `overviews` | Synthesis pages spanning multiple papers |

## Adding a New Paper

Follow the three-tier pipeline. One paper produces exactly one source file and one wiki file.

**Step 1 — Copy PDF to `papers/`**

Copy the PDF into `papers/` using the naming convention. Store it as a real file, never as a symlink.

**Step 2 — Create source summary in `sources/`**

Read the PDF, generate a summary, and save it as `sources/{stem}.md` using the schema below.

**Step 3 — Create or update the wiki page**

Add a paragraph to the relevant `wiki/{category}/` page, citing the paper. Update `wiki/index.md` and `wiki/log.md`.

---

## Source Schema: `sources/{stem}.md`

Every source file uses YAML frontmatter followed by a 7-section body.

```markdown
---
source_collection: papers
status: unread | reading | summarized | reviewed
paper_type: research | review | dataset | method
pdf_filename: "{author}-{year}-{title-5-words}.pdf"
pdf_path: "papers/{author}-{year}-{title-5-words}.pdf"
added_date: YYYY-MM-DD
read_date:
url:
code_url:
doi:
authors:
title:
journal:
year:
volume:
pages:
publisher:
abstract:
tags:
---

## Paper Summary

## Key Contributions

## Problem Statement

## Methodology

## Results

## Limitations & Future Work

## Personal Notes
```

**Frontmatter rules:**
- `source_collection`: always `papers` for external papers.
- `status`: `unread` → `reading` → `summarized` → `reviewed`. Update as you progress.
- `pdf_path` must point to a real file inside `papers/`.
- `added_date` is the day the file is created.
- Leave empty fields blank (`read_date:`, `url:`) rather than omitting them.

**Body rules:**
- Keep each section concise. The source is a working summary, not the final article.
- Use bullet points for key contributions and results.
- Personal notes are free-form; use them for connections, questions, and follow-ups.

---

## Wiki Schema: `wiki/{category}/{stem}.md`

Wiki pages are the final, synthesized form of knowledge. They reference one or more sources.

```markdown
---
category: camera-calib | lidar-calib | radar-calib | multi-sensor | slam | targetless-calib | online-calib | concepts | overviews
source_papers:
  - "{author}-{year}-{title-5-words}"
related_pages:
  - "wiki/{category}/{another-stem}.md"
tags:
  - tag-name
last_updated: YYYY-MM-DD
---

# {Title of the Wiki Page}

## Overview

## Key Methods / Concepts

## Related Papers

## Connections & Compounding

## Open Questions
```

**Frontmatter rules:**
- `category`: must match one of the directory names.
- `source_papers`: list every source this page draws from. Use the exact stem.
- `related_pages`: cross-link to other wiki pages for compounding.
- `last_updated`: update every time the page is edited.

**Body rules:**
- Write in complete sentences. This is the public-facing knowledge base.
- Cite sources inline: `Author et al. (Year)`.
- The Connections & Compounding section is mandatory for `overviews/` and encouraged everywhere else.

---

## PDF Management Rules

- **Store every PDF as a real file in `papers/`.** Never use symlinks.
- When a user provides a PDF from an external path, `cp` it into `papers/` and rename it to match the convention.
- `pdf_path` in source frontmatter must always point to `papers/{stem}.pdf`.
- If a PDF is missing, mark `status: unread` and set `pdf_path: ""` until it is acquired.

---

## Knowledge Compounding

The value of this wiki is not in isolated summaries but in synthesized understanding.

- **Cross-link aggressively.** Every wiki page should reference related pages in `related_pages`.
- **Build overview pages.** `wiki/overviews/` exists for compounding. Use it to connect methods across papers, compare approaches, and trace the evolution of ideas.
- **Update on read.** When you add a new paper, revisit existing wiki pages in the same category. Add connections rather than siloing the new entry.

---

## Obsidian Browsing

This repository is designed to be opened in [Obsidian](https://obsidian.md/).

- All internal links use standard Markdown `[[wiki/{category}/{stem}]]` or relative paths.
- The `wiki/` folder is the vault root for content browsing.
- Use graph view to discover connection gaps.
- Use tags for thematic traversal across categories.

---

## Design Principles

1. **One paper = one source file = one wiki file.** Never duplicate a paper across multiple source files.
2. **Never search the web.** The only permitted sources are files in this repository.
3. **Sources are working drafts; wiki pages are finished articles.** Source files can be rough. Wiki pages must be coherent, cited, and cross-linked.
4. **Preserve the stem.** PDF, source, and wiki files for the same paper must share the exact same `{author}-{year}-{title-5-words}` stem.
5. **Log every addition.** `wiki/log.md` records what was added, when, and why.
6. **English only in files.** All committed content is in English. Conversation can be in any language.

---

## `wiki/index.md` Format

The index is the entry point. It lists categories and their key papers.

```markdown
# Index

## camera-calib
- [Geometric camera calibration using circular targets](wiki/camera-calib/example-2020-geometric-camera-calibration.md) — A robust method for high-precision camera calibration using circular control points.
- [Automatic lens distortion correction](wiki/camera-calib/example-2019-automatic-lens-distortion.md) — Deep learning approach to correct radial and tangential distortion without checkerboards.
- [Multi-camera rig extrinsic calibration](wiki/camera-calib/example-2021-multi-camera-rig.md) — Joint calibration of stereo and surround-view camera rigs with minimal overlap.

## lidar-calib
- ...

## slam
- ...
```

## `wiki/log.md` Format

The log is a chronological record of work.

```markdown
# Log

## 2026-05-12
- Added source: `geiger-2012-are-we-ready-for.md` — KITTI dataset overview paper.
- Created wiki: `wiki/slam/geiger-2012-are-we-ready-for.md` — Added to SLAM category for benchmark context.
- Updated overview: `wiki/overviews/lidar-slam-datasets.md` — Linked KITTI entry with Oxford RobotCar and nuScenes.

## 2026-05-10
- Added source: `zhang-2014-lidar-camera-calibration.md` — Targetless extrinsic calibration via edge alignment.
- Created wiki: `wiki/lidar-calib/zhang-2014-lidar-camera-calibration.md` — Documented edge-based loss and initialization strategy.

## 2026-05-08
- Added source: `kummerle-2013-g2o-a-general-framework.md` — g2o optimization framework paper.
- Created wiki: `wiki/concepts/kummerle-2013-g2o-a-general-framework.md` — Added to concepts as foundational SLAM optimization background.
```
