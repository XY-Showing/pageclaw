# pageclaw-batch-test

End-to-end test for the `pageclaw-batch` skill. Runs the full batch pipeline on a small fixture matrix (3 styles) and a shared page-story, then validates all outputs and prints a structured test report.

## How to use

```
/pageclaw-batch-test
```

Run from the pageclaw repo root. All artifacts go to `test-output/batch/` (not the default `output/`).

## Fixtures

- `fixtures/style-matrix-test.yaml` -- 3 styles: sidebar-brutalist, centered-minimal, topnav-editorial
- Page-story reused from `skills/pageclaw-test/fixtures/page-story-test.md`

## Requirements

- Claude Code with ECC
- `pageclaw-batch` skill installed
- `pageclaw-test` skill installed (provides the shared page-story fixture)
