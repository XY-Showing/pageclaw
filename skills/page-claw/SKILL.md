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

**Before invoking any skill, ask the user two or three questions — no more. The number depends on whether they have a reference design (see below).**

1. **Reference design** — Ask this first, before generating any style options:

   > "Do you have a website or design you'd like to reference? (URL or screenshot — skip if not. Feel free to add a directional note, e.g. 'like this but darker' or 'same vibe, more minimal'.)"

   **If the user provides a reference URL:** fetch and analyze it immediately (before asking anything else):

   - **Fetch (max 2 pages)** — Use WebFetch on the provided URL. Then inspect `<nav>` links to identify site type: if it's a personal/academic page, the home page is sufficient; if it's a portfolio or company site, also fetch the single most relevant subpage (`/work`, `/about`, `/research`). Never fetch more than 2 pages.

   - **Extract design signals from HTML/CSS** — focus only on visual language, not layout structure or section order:
     - Color temperature (warm/neutral/cool) — from CSS color values
     - Typography character (serif/sans/mono, weight contrast) — from `font-family`, `font-weight`
     - Spatial density (compact/airy) — from `padding`, `line-height`
     - Animation presence — from `transition`, `@keyframes` (signals static/minimal vs. motion-enhanced)
     - Hover character — from `:hover` styles

   - **Document** — write extracted signals in the design doc under a `### Reference` sub-section (e.g., "From reference: monospace type, tight line-height, neutral palette, minimal hover transitions").

   After analysis, **skip Q2 and go directly to Q3.** The reference signals and any directional note the user added serve as the direction anchor for Q3 aesthetic option generation.

   **If the user skips:** proceed to Q2.

   The reference shapes palette and typography bias only. It does not control section order, layout structure, or anything governed by the page-story or Constraints.

2. **Visual direction** — *Ask only if Q1 was skipped.* Present 3–4 named style options labeled A/B/C/D, each with a one-line description. Include at least one distinctive or bold direction alongside safer choices. Tell the user: "If none feel right, just say so and I'll generate another set."

   Example:
   A. Warm & editorial — ...
   B. Cool & minimal — ...
   C. High-contrast & typographic — ...
   D. Bold & expressive — ...

3. **Aesthetic style** — Generate 4 aesthetic style options dynamically based on the page-story content, Q2 direction (if asked), and reference signals + directional note (if provided). Each option must be a genuinely different CSS world — not a variation of the same mood. Present options labeled A/B/C/D. Format:

   A. **Style Name** — one-sentence description
      Layout: [layout pattern]

   The CSS signature (3 key decorative properties) is your internal knowledge for this option — do not show it to the user. After the user selects an option, record the full CSS signature in the design doc under Aesthetic Implementation.

   Rules:
   - Options must be radically distinct from each other (e.g., Brutalism vs. Glassmorphism — not "cool minimal" vs. "refined minimal")
   - Include at least one unexpected direction for this subject matter
   - Each option's layout pattern must differ from at least one other option — variety in layout is part of variety in aesthetic
   - If user says "none feel right," generate a new set

   Example format (content must vary per page-story + direction inputs — these are illustrative, not a fixed menu):
   A. **Brutalist Academic** — Raw grid, stark contrast, no decoration; reading-machine feel
      Layout: asymmetric two-column grid, content bleeds full width
   B. **Glassmorphism Light** — Frosted glass panels, layered translucency, modern tech feel
      Layout: stacked frosted cards, single centered column
   C. **Museum Whitespace** — Extreme negative space, caption-driven, artifact-display feel
      Layout: narrow single column, wide margins, content as artifact
   D. **Terminal Scholar** — Monospace throughout, dark mode, command-line aesthetic
      Layout: sticky sidebar left, scrollable main right

Infer everything else (audience, tone, content hierarchy) directly from the page-story. Do not ask additional questions beyond these.

---

Once you have the user's answers (and have analyzed any reference URL), write a brief summary in your response — the user's aesthetic choice, reference signals extracted (or that they skipped), and the target save path. This appears in the conversation history so teach-impeccable can read it without re-asking. Then **invoke the `teach-impeccable` skill using the Skill tool**, passing the target file path (`docs/plans/YYYY-MM-DD-<name>-design.md`) as the `config_file` argument. teach-impeccable scans the page-story and produces a `## Design Context` block (users, brand personality, aesthetic direction, design principles).

Save output to: `docs/plans/YYYY-MM-DD-<name>-design.md`

## Constraints

**The page-story is the authoritative source.** Render it faithfully — do not add, remove, reorder, or reinterpret its content. The design system serves the story, not the other way around. Content must remain in its original section — do not promote elements (e.g. a bold paragraph) into a hero area, header badge, or any other section.

**Design decisions must derive solely from the page-story and design context gathered in Step 1.** Do not reference existing project files, other HTML pages, or prior designs for inspiration unless the user explicitly requests it.

## Step 2 — Design System

**Invoke the `ui-ux-pro-max` skill using the Skill tool with `--design-system`.** This step must be performed by the skill — do not write a design system manually, even if the skill's output seems mismatched to the context. Use the page-story content and design context from Step 1 as the sole inputs.

Take the skill's output (palette, typography, style, effects, anti-patterns) as the foundation. Where specific recommendations conflict with the design context (e.g. a "motion-driven" style for an academic page), note the override and the reason in the design doc, then adapt those elements. The rest of the skill's output applies as-is. Append the result as a new `## Design System` section to the design doc from Step 1.

**The design system must include a `### Aesthetic Implementation` section** that translates the chosen aesthetic style into concrete CSS patterns. This is the bridge that makes the style choice executable — writing-plans reads it to generate specific CSS, not generic defaults.

Required fields:
- **Layout structure** — the page layout this aesthetic naturally produces: describe the HTML skeleton (e.g., sticky sidebar + scrollable main, single centered column, asymmetric grid). This comes directly from the layout descriptor in the aesthetic CSS signature and drives the HTML structure in writing-plans.
- **Surface treatment** — exact CSS for cards, panels, containers (border, shadow, border-radius, background)
- **Typography expression** — heading vs. body distinction: weight ratio, size scale, letter-spacing
- **Decorative rules** — what decoration is present / explicitly forbidden in this aesthetic
- **Spatial rhythm** — density disposition this aesthetic produces (compact / airy / extreme whitespace / dense)
- **Signature CSS** — 3–5 declarations that are the unmistakable fingerprint of this aesthetic (copied from the aesthetic CSS signature, expanded). This is the CSS signature you generated internally for the chosen aesthetic option — record it here in full even though it was not shown to the user during the aesthetic question.

## Step 3 — Implementation Plan

**Invoke the `writing-plans` skill using the Skill tool.** Do not write the plan manually. Use the design doc as spec. Output: a task-by-task implementation plan saved to `docs/plans/YYYY-MM-DD-<name>-impl.md`.

## Rendering Conventions

These conventions control how certain page-story sections are visually rendered during Build (Step 4). Content (what links exist, what text says) is never changed — only the presentation.

**Prose is the absence of a design decision.** Every content element has a semantic type — a record, a sequence, a label, a relationship. Plain text collapses all types into the same form. Before building, ask of each element: *what is this, structurally?* Then surface that structure visually. A sequence in time is a timeline. A categorical attribute is a badge. A name among others marks a relationship. These details are what separate a polished page from a generic one — default to prose only for genuinely unstructured narrative.

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
- [ ] Aesthetic style from design doc is reflected in CSS (not generic clean/modern defaults)

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
