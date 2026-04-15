---
name: pageclaw-batch-test
description: Run an automated end-to-end test of the pageclaw-batch pipeline using bundled fixtures. Validates batch generation with a small style matrix. Use to verify pageclaw-batch behavior after changes.
license: MIT
metadata:
  author: pageclaw
  version: "1.0.0"
  homepage: https://github.com/XY-Showing/pageclaw
---

# Pageclaw Batch -- End-to-End Test

Run the full `pageclaw-batch` pipeline on bundled fixtures and produce a test report.

## Fixtures

- **Style matrix:** `skills/pageclaw-batch-test/fixtures/style-matrix-test.yaml`
- **Page-story:** `skills/pageclaw-test/fixtures/page-story-test.md` (shared with pageclaw-test)

Read both files at the start. These are the only inputs for the entire test run.

## Execution

1. **Override output directory.** All pageclaw-batch output must go to `test-output/batch/` instead of the default `output/`. Before invoking pageclaw-batch, create `test-output/batch/html/` and `test-output/batch/docs/`. When pageclaw-batch references `output/`, substitute `test-output/batch/` throughout.

2. **Invoke pageclaw-batch** with the test style matrix and the shared page-story. Run in autonomous mode -- do not wait for user confirmation at the summary table. The matrix contains 3 styles; expect 3 combinations.

3. **Do not ask the user any questions** during the run. If pageclaw-batch would normally ask for confirmation, proceed automatically.

## Validation

After the batch pipeline completes, run these checks against `test-output/batch/`.

### Pipeline checks

- Pre-flight: style matrix parsed, page-story found, output dirs created.
- Parse Inputs: 3 styles detected, 1 page-story, 3 combinations listed.
- Each style (`01-sidebar-brutalist`, `02-centered-minimal`, `03-topnav-editorial`): all steps A-E completed without error.
- Master Index: `test-output/batch/index.html` generated.

### Artifact checks (repeat for each style `<id>`)

- `test-output/batch/docs/<id>-design.md` exists, contains `## Design Context` and `## Design System`.
- `test-output/batch/docs/<id>-impl.md` exists, contains a task-by-task plan.
- `test-output/batch/html/<id>.html` exists, is valid HTML (has `<!DOCTYPE html>` or `<html`).
- HTML uses CSS custom properties (`--primary`, `--background`, etc.).
- HTML has a light/dark mode toggle.
- HTML is responsive (no fixed widths exceeding `100vw`).
- CSS color values match the style matrix colors for that entry.
- Layout structure matches the style matrix `layout` field.

### Master index checks

- `test-output/batch/index.html` exists.
- Contains links to all 3 generated HTML files.
- Has its own light/dark mode toggle.

## Test Report

Print the following report after validation:

```
## Pageclaw Batch Test Report

### Pipeline
- [ ] Pre-flight: passed / FAILED
- [ ] Parse Inputs: passed / FAILED
- [ ] Style 01-sidebar-brutalist: completed / FAILED
- [ ] Style 02-centered-minimal: completed / FAILED
- [ ] Style 03-topnav-editorial: completed / FAILED
- [ ] Master Index: generated / FAILED

### Artifact Checks
For each style <id>:
- [ ] output/docs/<id>-design.md exists and contains ## Design Context + ## Design System
- [ ] output/docs/<id>-impl.md exists and contains task-by-task plan
- [ ] output/html/<id>.html exists and is valid HTML
- [ ] HTML has CSS custom properties (--primary, --background, etc.)
- [ ] HTML has light/dark mode toggle
- [ ] HTML is responsive (no fixed widths > 100vw)
- [ ] Colors in CSS match style-matrix values
- [ ] Layout matches style-matrix layout field

### Master Index Check
- [ ] output/index.html exists
- [ ] Links to all generated HTML files
- [ ] Has light/dark mode toggle

### Violations
(list any issues, or "None")

### Notes
(observations about quality, timing, etc.)
```

Mark each item with a check (passed) or note the failure. After the report, open `test-output/batch/index.html` in the browser if possible.
