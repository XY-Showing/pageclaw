---
name: pageclaw-batch
description: "Batch generate static HTML page templates by combining a pre-defined style matrix (layout, aesthetic, colors, typography) with page-story markdown files. Skips interactive design questions. Input: style-matrix.yaml + page-story files. Output: organized HTML + design docs."
argument-hint: "[style-matrix-path] [page-stories-glob]"
license: MIT
metadata:
  author: pageclaw
  version: "1.0.0"
  homepage: https://github.com/XY-Showing/pageclaw
---

# Pageclaw Batch

Batch-generate polished static HTML pages from a style matrix and page-story files, bypassing all interactive design questions.

`<id>` in filenames below is the style entry's `id` field (e.g., `01-sidebar-brutalist`).

## Pipeline

```
style-matrix.yaml + page-story-*.md
         |
         v
  [Parse & Validate]
         |
         v
  For each (style, page-story) combination:
    |
    [A. Design Context]       -> output/docs/<id>-design.md
    |
    [B. ui-ux-pro-max]        -> (appended to design.md)
    |
    [C. writing-plans]        -> output/docs/<id>-impl.md
    |
    [D. Build]                -> output/html/<id>.html
    |
    [E. Quality Pass]         -> polish -> audit -> (quieter / critique if needed)
         |
         v
  [Generate Master Index]     -> output/index.html
```

## Skill Invocation

Steps B through E invoke sub-skills. How to invoke depends on your platform:

| Platform | How to invoke a sub-skill |
|----------|--------------------------|
| **Claude Code** | Use the `Skill` tool with the skill name |
| **Codex / OpenCode / Cursor / other** | Read the sub-skill's `SKILL.md` file in full, then execute it as a complete mandatory step -- produce all required outputs on disk before proceeding. Do NOT summarize, abbreviate, or simulate the output. |

Reading a skill's instructions and approximating the output is not equivalent to executing it. Each step must produce its defined artifact on disk before the next step begins.

---

## Pre-flight

1. Locate `style-matrix.yaml` (from argument, current directory, or user message). If not found, stop and ask the user.
2. Validate the YAML against the schema (`style-matrix.schema.yaml`). Every entry must have: `id`, `layout`, `aesthetic`, `colors` (all six fields), `typography` (all four fields), `decorative_rules`.
3. Locate page-story files (from argument glob, or `page-story-*.md` in current directory). At least one must exist.
4. Create output directories: `output/html/`, `output/docs/`.

Report to user: number of styles, number of page-stories, total combinations to generate.

---

## Phase 1 -- Parse Inputs

1. Read and parse `style-matrix.yaml`.
2. Glob page-story files matching the provided pattern.
3. Compute the combination list: each style paired with each page-story. For single page-story scenarios, this is just the list of styles.
4. Display a summary table to the user:

```
Styles: 6 | Page-stories: 1 | Combinations: 6
  01-sidebar-brutalist       x page-story-ying-xiao.md
  02-centered-minimal        x page-story-ying-xiao.md
  ...
```

Proceed only after user confirms (or immediately if running in autonomous mode).

---

## Phase 2 -- For Each Combination

For each (style entry, page-story) pair, execute Steps A through E sequentially. Derive `<id>` from the style entry's `id` field. Derive `<name>` from the page-story filename (e.g., `page-story-ying-xiao.md` -> `ying-xiao`).

### Step A -- Create Design Context

This step replaces page-claw's interactive Step 1. Instead of asking questions and invoking teach-impeccable, synthesize the design context directly from the style matrix entry.

Write `output/docs/<id>-design.md` with the following structure:

```markdown
# <aesthetic> -- <name>

## Design Context

### Users
Infer target audience from the page-story content (academic peers, recruiters, general visitors, etc.). State the primary and secondary audiences.

### Brand Personality
Derive from the aesthetic name and the page-story tone. State 3-5 adjectives.

### Aesthetic Direction
State the chosen aesthetic (`<aesthetic>` from matrix), its layout pattern (`<layout>`), and the overall mood it produces.

### Design Principles
3-4 principles that govern this aesthetic applied to this content. Derive from the intersection of the aesthetic style and the page-story subject matter.

### Reference
Color specification (light mode):
- Primary: <colors.primary>
- Secondary: <colors.secondary>
- Background: <colors.background>
- Surface: <colors.surface>
- Accent: <colors.accent>
- Text: <colors.text>

Typography:
- Headings: <typography.heading_font>, weight <typography.heading_weight>
- Body: <typography.body_font>, weight <typography.body_weight>

Decorative rules:
- <each entry from decorative_rules, one per line>

### Aesthetic Implementation

**Layout structure** -- Describe the HTML skeleton this layout produces (e.g., for `sticky-sidebar-left`: a fixed-width sidebar with `position: sticky` on the left, scrollable main content area on the right; for `single-centered-column`: a single `max-width` container centered with `margin: auto`).

**Surface treatment** -- Derive from colors and decorative_rules: what borders, shadows, backgrounds, and border-radii apply to cards, panels, and containers.

**Typography expression** -- Describe the heading vs. body distinction: weight contrast (<heading_weight> vs. <body_weight>), font pairing (<heading_font> vs. <body_font>), and size scale.

**Decorative rules** -- Restate the decorative_rules array as concrete CSS directives: what is present, what is explicitly forbidden.

**Spatial rhythm** -- Infer from the aesthetic name and layout: compact, airy, extreme whitespace, or dense.

**Signature CSS** -- 3-5 CSS declarations that are the unmistakable fingerprint of this aesthetic. Derive from the combination of layout, colors, typography, and decorative_rules.
```

All values come from the style matrix entry and the page-story. Do not ask the user any questions. Do not invoke teach-impeccable.

**Verify:** `output/docs/<id>-design.md` exists and contains `## Design Context` with all subsections above.

### Step B -- Design System

Invoke the `ui-ux-pro-max` skill with `--design-system`. Use the page-story content and the design context from Step A as inputs.

Append the skill's output as a `## Design System` section to `output/docs/<id>-design.md`. Where the skill's recommendations conflict with the matrix-specified colors, typography, or decorative rules, the matrix values take precedence -- note the override and reason in the doc.

**Verify:** `output/docs/<id>-design.md` contains both `## Design Context` and `## Design System`.

### Step C -- Implementation Plan

Invoke the `writing-plans` skill. Use the design doc as spec.

Output: `output/docs/<id>-impl.md` containing a task-by-task build plan.

**Verify:** `output/docs/<id>-impl.md` exists and contains a task-by-task plan.

### Step D -- Build

Execute the implementation plan. The plan drives all structural and visual decisions.

Before marking the build complete, verify:

- [ ] All colors use CSS custom properties with both light and dark theme values
- [ ] Light/dark mode toggle button (sun/moon icon, top-right corner) with `localStorage` persistence and `prefers-color-scheme` detection as initial default
- [ ] All interactive elements have `:hover` and `:focus-visible` states
- [ ] Typography hierarchy: at least 3 distinct size/weight levels
- [ ] Consistent spacing rhythm throughout
- [ ] Responsive: no horizontal scroll at 375px or 1200px
- [ ] Rendering Conventions applied (see below)
- [ ] Aesthetic style from design doc reflected in CSS, not generic defaults
- [ ] Layout matches the `layout` field from the style matrix

Output: `output/html/<id>.html`

### Step E -- Quality Pass

Invoke these skills in order:

- `polish` -- always run.
- `audit` -- always run.
- `quieter` -- only if the design feels visually aggressive after polish.
- `critique` -- only if quality concerns remain after polish and audit.

---

## Phase 3 -- Generate Master Index

After all combinations are built, generate `output/index.html` -- a styled HTML page that:

1. Lists every generated template with its aesthetic name, layout type, and a link to the HTML file.
2. Groups entries by page-story if multiple page-stories were used.
3. Shows the color palette for each entry as small swatches.
4. Includes its own light/dark mode toggle.
5. Uses clean, minimal styling (not one of the matrix aesthetics).

---

## Rendering Conventions

Apply the same rendering conventions defined in the `page-claw` skill:

- **Prose-to-structure mapping:** Before building, identify the semantic type of each content element (timeline, badge, relationship, etc.) and render it structurally rather than as plain text.
- **Links section:** A `## Links` section must render as icon-based links using inline SVG from Simple Icons CDN (`https://cdn.jsdelivr.net/npm/simple-icons@latest/icons/<slug>.svg`). Email uses a generic envelope SVG. Unrecognized platforms use a generic chain-link SVG.

Refer to `page-claw/SKILL.md` "Rendering Conventions" section for the full platform slug table and implementation details.

---

## Constraints

- **The page-story is the authoritative source.** Render it faithfully -- do not add, remove, reorder, or reinterpret its content. The design system serves the story, not the other way around.
- **Design decisions derive from the style matrix and page-story only.** Do not reference existing project files, other HTML pages, or prior designs unless the user explicitly requests it.
- **Matrix values are authoritative for colors, typography, and decorative rules.** When sub-skills (ui-ux-pro-max) produce conflicting values, override with the matrix and document the reason.

---

## Error Handling

If a combination fails at any step:

1. Log the failure: which combination (`<id>` x `<page-story>`), which step, and the error.
2. Write a partial marker file `output/docs/<id>-error.log` with the failure details.
3. Continue with the next combination -- do not abort the entire batch.
4. After the batch completes, report all failures in a summary to the user.

---

## Intermediate Artifacts

| Artifact | Created by | Purpose |
|----------|-----------|---------|
| `output/docs/<id>-design.md` | Step A + Step B | Design context + system, source of truth for all decisions |
| `output/docs/<id>-impl.md` | Step C | Task-by-task build instructions |
| `output/html/<id>.html` | Step D | Final HTML template |
| `output/docs/<id>-error.log` | Error handler | Failure details for failed combinations |
| `output/index.html` | Phase 3 | Master index linking all generated templates |
