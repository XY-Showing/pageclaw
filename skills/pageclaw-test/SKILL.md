---
name: pageclaw-test
description: Run a full automated end-to-end test of the page-claw pipeline using a bundled fixture page-story. Simulates user responses autonomously. Use when you want to verify page-claw behavior after making changes to the skill.
license: MIT
metadata:
  author: pageclaw
  version: "1.0.0"
  homepage: https://github.com/XY-Showing/pageclaw
---

# Page Claw — End-to-End Test

Run the full `page-claw` pipeline on the bundled fixture page-story, simulating user responses autonomously, and produce a test report.

## Fixture

Page-story location: `skills/pageclaw-test/fixtures/page-story-test.md` (relative to the pageclaw repo root, or the equivalent installed path under `~/.claude/skills/pageclaw-test/fixtures/`).

Read this file at the start. This is the page-story for the entire test run.

## Simulated User Response Rules

When page-claw asks questions, respond as a simulated user using these rules — do not break character or explain what you're doing until the pipeline is complete:

| Question type | Response strategy |
|---------------|-------------------|
| Multiple choice (options provided) | Pick one option at random |
| Open-ended (no options given) | Ask: "What are my options?" — wait for options, then pick one at random |
| Skip/optional (e.g. reference design) | Always respond: "Skip" |
| Any question beyond the expected flow (see Behavior Checks) | Record it as a **violation** and respond: "Skip" |

## Outputs

Save all pipeline artifacts to `test-output/` in the current working directory:

- `test-output/design.md` — design context + design system (Steps 1–2)
- `test-output/impl.md` — implementation plan (Step 3)
- `test-output/index.html` — final page (Step 4)

Do not save to `docs/plans/` — this is a test run, not a real project.

## Test Report

After the pipeline completes, print a test report in this format:

```
## Page Claw Test Report

### Pipeline
- [ ] Step 1 — Design Context: completed / FAILED
- [ ] Step 2 — Design System: completed / FAILED
- [ ] Step 3 — Implementation Plan: completed / FAILED
- [ ] Step 4 — Build: completed / FAILED
- [ ] Step 5 — Quality pass: completed / skipped / FAILED

### Behavior Checks
- [ ] Q1 was skip-optional (reference design)
- [ ] Q2 was multiple choice with ≥3 options (visual direction) — only present if Q1 was skipped
- [ ] Q3 was multiple choice with 4 options (aesthetic style) — always present
- [ ] No additional questions asked beyond Q1 + Q2 (if applicable) + Q3
- [ ] No references to existing project files in design decisions

### Violations
(list any violations found, or "None")

### Output
- design.md: saved / missing
- impl.md: saved / missing
- index.html: saved / missing

### Notes
(any observations about output quality, faithfulness to page-story, etc.)
```

Mark each item as passed (check) or failed (with a brief note). After the report, open `test-output/index.html` in the browser if possible.
