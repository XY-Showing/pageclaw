# page-claw

Turn a structured markdown brief (`page-story.md`) into a polished, single-file static HTML page — design context, design system, implementation plan, build, and quality pass, all in one command.

## What it does

`/page-claw` runs a five-step pipeline:

1. **Design Context** — asks up to 3 questions (reference URL, visual direction, aesthetic style) and writes a design doc
2. **Design System** — generates palette, typography, spacing, and aesthetic implementation spec
3. **Implementation Plan** — produces a task-by-task build plan
4. **Build** — writes `index.html` following the plan; verifies responsive layout, icon links, hover/focus states
5. **Quality Pass** — runs `polish` then `audit` to catch alignment, accessibility, and anti-pattern issues

No external API keys or binary dependencies required.

## How to use

```
/page-claw page-story.md
```

If no page-story file is found, the skill creates `page-story.md` in your current directory with sample content (fictional academic Lena Hartmann) so you can preview the output immediately.

## page-story format

A page-story is a plain markdown file with section headers and bullet lists. Sections supported:

- `## About Me` — bio text + avatar image reference
- `## Links` — social/profile URLs (rendered as inline SVG icons)
- `## News` — bullet list of milestones
- `## Selected Publications` — bullet list of paper entries
- `## Preprints` — bullet list of working papers
- `## Invited Talk` — bullet list of talk entries

See `page-story-starter.md` (installed alongside this skill) for a complete example.

## Output

- `index.html` — self-contained static page, no build step required
- `docs/plans/YYYY-MM-DD-<name>-design.md` — design context + system
- `docs/plans/YYYY-MM-DD-<name>-impl.md` — implementation plan

## Requirements

- Claude Code
- ECC (Everything Claude Code) — for skill installation
