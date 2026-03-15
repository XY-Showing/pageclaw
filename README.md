# page-claw

**Turn a markdown story into a polished static HTML page — with design intelligence baked in.**

page-claw is an AI agent skill that converts `page-story-*.md` files into production-ready, single-file static HTML pages. It orchestrates a full pipeline: design context gathering, evidence-based design system generation, implementation planning, and post-build quality review.

Built for personal academic pages, professional profiles, and project pages. Designed to extend to any markdown-to-page use case.

Works with Claude Code, Codex, Cursor, and any agent that supports markdown-based skill/instruction files.

---

## How It Works

```
page-story.md
    │
    ▼
[1. teach-impeccable]     → gathers design context through dialogue
    │
    ▼
[2. ui-ux-pro-max]        → generates design system (palette, fonts, style)
    │
    ▼
[3. writing-plans]        → produces a task-by-task implementation plan
    │
    ▼
[4. Build]                → index.html (single file, no build step)
    │
    ▼
[5. Quality pass]         → quieter / polish / audit / critique
```

Each step produces an intermediate artifact in `docs/plans/`. Design decisions are evidence-based and traceable — not hardcoded in the skill.

---

## Prerequisites

- Python 3 (for `ui-ux-pro-max` design system generation)
- An AI coding agent (see Installation below)

---

## Installation

```bash
git clone https://github.com/XY-Showing/pageclaw
```

Then install for your agent:

### Claude Code

```bash
cp -r pageclaw/skills/* ~/.claude/skills/
```

Invoke with `/page-claw` in any Claude Code session.

### Codex

See [`.codex/INSTALL.md`](.codex/INSTALL.md) for symlink-based setup (supports `git pull` updates).

### OpenCode

See [`.opencode/INSTALL.md`](.opencode/INSTALL.md) for symlink-based setup (supports `git pull` updates).

### Cursor

Copy skill content into your `.cursor/rules/` directory as `.mdc` files, or reference them in your system prompt.

### Other agents

Each skill is a self-contained markdown file (`SKILL.md`). Copy the content into your agent's context, rules, or instruction file as needed.

---

## Quick Start

1. Create a `page-story-*.md` file with your content (About, Publications, Links, etc.)
2. Open your agent in the project directory
3. Invoke the `page-claw` skill
4. Follow the prompts — the agent will ask 3–4 design questions, generate a design system, write the plan, and build the page

**Output:** `index.html` — open directly in any browser, no server needed.

---

## Skills

| Skill | Source | Purpose |
|-------|--------|---------|
| `page-claw` | This project | Orchestrates the full pipeline |
| `teach-impeccable` | Impeccable | One-time design context gathering |
| `frontend-design` | Impeccable | Production-grade frontend implementation reference |
| `audit` | Impeccable | Accessibility, performance, and anti-pattern audit |
| `polish` | Impeccable | Final quality pass before shipping |
| `quieter` | Impeccable | Tone down visually aggressive designs |
| `critique` | Impeccable | Comprehensive UX/design evaluation |
| `ui-ux-pro-max` | UI/UX Pro Max | Design system generation (palette, fonts, style) |
| `design-system` | UI/UX Pro Max | Design system persistence and retrieval |
| `design` | UI/UX Pro Max | General design guidance |
| `ui-styling` | UI/UX Pro Max | UI styling patterns |

---

## File Structure

```
page-claw/
├── README.md
└── skills/
    ├── page-claw/          # Main orchestrating skill
    ├── teach-impeccable/   # from Impeccable
    ├── frontend-design/    # from Impeccable (+ 7 reference files)
    ├── audit/              # from Impeccable
    ├── polish/             # from Impeccable
    ├── quieter/            # from Impeccable
    ├── critique/           # from Impeccable
    ├── ui-ux-pro-max/      # from UI/UX Pro Max (+ scripts + data)
    ├── design-system/      # from UI/UX Pro Max
    ├── design/             # from UI/UX Pro Max
    └── ui-styling/         # from UI/UX Pro Max
```

---

## License

MIT

---

## Acknowledgements

This project incorporates sub-skills from two open-source libraries:

### Impeccable
- **Repository:** https://github.com/pbakaus/impeccable
- **Author:** Paul Bakaus (@pbakaus)
- **License:** Apache 2.0
- **Skills used:** `teach-impeccable`, `frontend-design`, `audit`, `polish`, `quieter`, `critique`

### UI/UX Pro Max
- **Repository:** https://github.com/nextlevelbuilder/ui-ux-pro-max-skill
- **Author:** nextlevelbuilder
- **Skills used:** `ui-ux-pro-max`, `design-system`, `design`, `ui-styling`

Sub-skills are copied verbatim from their respective repositories without modification.
