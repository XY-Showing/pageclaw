---
name: page-claw
description: "Use when converting a page-story markdown file into a static HTML page. Covers the full pipeline: design context gathering, design system generation, and implementation planning."
argument-hint: "[page-story-file]"
license: MIT
metadata:
  author: pageclaw
  version: "1.0.0"
---

# Page Claw

Convert a `page-story-*.md` file into a polished, single-file static HTML page.

## Pipeline

```
page-story.md
    │
    ▼
[1. teach-impeccable]       → design-context.md
    │
    ▼
[2. ui-ux-pro-max]          → YYYY-MM-DD-<name>-design.md
    │
    ▼
[3. writing-plans]          → YYYY-MM-DD-<name>-impl.md
    │
    ▼
[4. Build]                  → index.html
    │
    ▼
[5. Quality pass]           → quieter / polish / audit / critique
```

## Step 1 — Design Context

Run `teach-impeccable`. It scans the page-story and asks focused questions to produce a `## Design Context` block (users, brand personality, aesthetic direction, design principles).

Save output to: `docs/plans/YYYY-MM-DD-<name>-design.md`

## Step 2 — Design System

Run `ui-ux-pro-max --design-system` using keywords derived from the page-story content and design context. The output (palette, typography, style, effects, anti-patterns) gets appended to the design doc from Step 1.

If the recommended style conflicts with stated user preferences, note the override and reasoning in the design doc.

## Step 3 — Implementation Plan

Run `writing-plans` using the design doc as spec. Output: a task-by-task implementation plan saved to `docs/plans/YYYY-MM-DD-<name>-impl.md`.

## Step 4 — Build

Execute the implementation plan. The plan drives all structural and visual decisions — do not deviate from it without updating the plan doc first.

## Step 5 — Quality Pass

After `index.html` is functionally complete, run in order as needed:

- `quieter` — if design feels visually aggressive
- `polish` — final pass for alignment, states, edge cases
- `audit` — accessibility, performance, anti-pattern report
- `critique` — overall UX/design evaluation

## Intermediate Artifacts

| Artifact | Created by | Purpose |
|----------|-----------|---------|
| `docs/plans/YYYY-MM-DD-*-design.md` | teach-impeccable + ui-ux-pro-max | Design context + system, source of truth for all decisions |
| `docs/plans/YYYY-MM-DD-*-impl.md` | writing-plans | Task-by-task build instructions |
| `index.html` | Build step | Final deliverable |
