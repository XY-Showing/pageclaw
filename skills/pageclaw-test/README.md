# pageclaw-test

End-to-end test for the `page-claw` skill — runs the full pipeline on a bundled fixture page-story, simulates user responses autonomously, and prints a structured test report.

## What it does

`/pageclaw-test` exercises the complete `page-claw` pipeline without human input:

1. Reads the bundled fixture (`fixtures/page-story-test.md` — fictional academic Lena Hartmann)
2. Invokes `/page-claw` and simulates user responses: skips optional questions, picks random options for multiple-choice
3. Saves all artifacts to `test-output/` in the current working directory
4. Prints a pass/fail report covering pipeline steps and behavior checks

Use this after modifying `page-claw` to verify nothing broke.

## How to use

```
/pageclaw-test
```

Run from the pageclaw repo root (or any directory — artifacts always go to `test-output/` relative to the working directory).

## Outputs

| File | Contents |
|------|----------|
| `test-output/design.md` | Design context + design system (Steps 1–2) |
| `test-output/impl.md` | Implementation plan (Step 3) |
| `test-output/index.html` | Final rendered page (Step 4) |

## Fixture

`fixtures/page-story-test.md` — Lena Hartmann, 4th-year PhD student at Westgate University, AI safety/fairness research. Fictional character, no legal risk.

`fixtures/avatar.jpg` — placeholder avatar image for the fixture page.

## Requirements

- Claude Code
- ECC (Everything Claude Code)
- `page-claw` skill installed
