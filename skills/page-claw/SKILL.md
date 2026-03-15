---
name: page-claw
description: "Use when the user wants to turn a page-story markdown file (page-story-*.md) into a polished static HTML page. Trigger for: personal pages, academic homepages, portfolio pages, profile pages, any request to build a page from a structured markdown brief. Runs the full pipeline automatically: design context gathering, design system, implementation plan, build, and quality pass."
argument-hint: "[page-story-file]"
license: MIT
metadata:
  author: pageclaw
  version: "1.0.0"
---

# Page Claw

Convert a `page-story-*.md` file into a polished, single-file static HTML page.

`<name>` in filenames below is derived from the page-story filename (e.g., `page-story-ying-xiao.md` → `ying-xiao`). If the filename has no slug, use the subject's name from the content in kebab-case.

## Pipeline

```
page-story.md
    │
    ▼
[1. teach-impeccable]       → YYYY-MM-DD-<name>-design.md
    │
    ▼
[2. ui-ux-pro-max]          → (appended to design.md)
    │
    ▼
[3. writing-plans]          → YYYY-MM-DD-<name>-impl.md
    │
    ▼
[4. Build]                  → index.html (project root)
    │
    ▼
[5. Quality pass]           → polish → audit → (quieter / critique if needed)
```

## Step 1 — Design Context

**Before invoking any skill, ask the user exactly two questions — no more:**

1. **Visual direction** — Present 3–4 named style options, each with a one-line description. Include at least one distinctive or bold direction alongside safer choices (e.g. "Warm & editorial", "Cool & minimal", "High-contrast & typographic", "Bold & expressive"). Tell the user: "If none feel right, just say so and I'll generate another set."
2. **Reference design** — "Do you have a website or design you'd like to reference? (URL or screenshot — skip if not)"

Infer everything else (audience, tone, content hierarchy) directly from the page-story. Do not ask additional questions.

Once you have the user's answers, write a brief summary in your response — the user's style choice, their reference (or that they skipped), and the target save path. This appears in the conversation history so teach-impeccable can read it without re-asking. Then **invoke the `teach-impeccable` skill using the Skill tool**, passing the target file path (`docs/plans/YYYY-MM-DD-<name>-design.md`) as the `config_file` argument. teach-impeccable scans the page-story and produces a `## Design Context` block (users, brand personality, aesthetic direction, design principles).

Save output to: `docs/plans/YYYY-MM-DD-<name>-design.md`

## Constraints

**The page-story is the authoritative source.** Render it faithfully — do not add, remove, reorder, or reinterpret its content. The design system serves the story, not the other way around. Content must remain in its original section — do not promote elements (e.g. a bold paragraph) into a hero area, header badge, or any other section.

**Design decisions must derive solely from the page-story and design context gathered in Step 1.** Do not reference existing project files, other HTML pages, or prior designs for inspiration unless the user explicitly requests it.

## Step 2 — Design System

**Invoke the `ui-ux-pro-max` skill using the Skill tool with `--design-system`.** This step must be performed by the skill — do not write a design system manually, even if the skill's output seems mismatched to the context. Use the page-story content and design context from Step 1 as the sole inputs.

Take the skill's output (palette, typography, style, effects, anti-patterns) as the foundation. Where specific recommendations conflict with the design context (e.g. a "motion-driven" style for an academic page), note the override and the reason in the design doc, then adapt those elements. The rest of the skill's output applies as-is. Append the result as a new `## Design System` section to the design doc from Step 1.

## Step 3 — Implementation Plan

**Invoke the `writing-plans` skill using the Skill tool.** Do not write the plan manually. Use the design doc as spec. Output: a task-by-task implementation plan saved to `docs/plans/YYYY-MM-DD-<name>-impl.md`.

## Rendering Conventions

These conventions control how certain page-story sections are visually rendered during Build (Step 4). Content (what links exist, what text says) is never changed — only the presentation.

**Links section** — A `## Links` section containing profile/social URLs must be rendered as icon-based links using inline SVG, not bare text. Each icon links to its URL with an accessible `aria-label`.

Use [Simple Icons](https://simpleicons.org/) as the icon source via CDN:
`https://cdn.jsdelivr.net/npm/simple-icons@latest/icons/<slug>.svg`

Common platform slugs:

| Platform | Slug |
|----------|------|
| GitHub | `github` |
| LinkedIn | `linkedin` |
| Google Scholar | `googlescholar` |
| rednote (小红书) | `xiaohongshu` |
| Twitter / X | `x` |
| ORCID | `orcid` |
| ResearchGate | `researchgate` |

For email (`mailto:` links), use a generic envelope SVG (not from Simple Icons). For any unrecognized platform, use a generic chain-link SVG — do not guess with semantically unrelated icons (location pin, bookmark, globe, etc.).

## Step 4 — Build

Execute the implementation plan. The plan drives all structural and visual decisions — do not deviate from it without updating the plan doc first.

Before marking the build complete, verify:

- [ ] All interactive elements have hover and focus states (`:hover`, `:focus-visible`)
- [ ] Typography hierarchy is clear — at least 3 distinct size/weight levels
- [ ] Spacing follows a consistent rhythm throughout
- [ ] Page is responsive: no horizontal scroll at 375px or 1200px
- [ ] Rendering Conventions have been applied (e.g. icon links for `## Links`)

## Step 5 — Quality Pass

After `index.html` is functionally complete, invoke these skills using the Skill tool in order:

- `polish` — **always run.** Final pass for alignment, states, edge cases.
- `audit` — **always run.** Accessibility, performance, anti-pattern report.
- `quieter` — only if the design feels visually aggressive after polish.
- `critique` — only if quality concerns remain after polish and audit.

## Intermediate Artifacts

| Artifact | Created by | Purpose |
|----------|-----------|---------|
| `docs/plans/YYYY-MM-DD-*-design.md` | teach-impeccable + ui-ux-pro-max | Design context + system, source of truth for all decisions |
| `docs/plans/YYYY-MM-DD-*-impl.md` | writing-plans | Task-by-task build instructions |
| `index.html` | Build step | Final deliverable |
