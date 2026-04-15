---
name: pageclaw-batch
description: "Batch generate static HTML page templates by combining a style matrix (design intents + layouts) with page-story markdown files. Skips interactive design questions — each style entry provides a design intent description that drives the full pipeline. Input: style-matrix.yaml + page-story files. Output: organized HTML + design docs."
argument-hint: "[style-matrix-path] [page-stories-glob]"
license: MIT
metadata:
  author: pageclaw
  version: "2.0.0"
  homepage: https://github.com/XY-Showing/pageclaw
---

# Pageclaw Batch

Batch-generate polished static HTML pages from a style matrix and page-story files, bypassing all interactive design questions.

`<id>` in filenames below is the style entry's `id` field (e.g., `42-col-editorial-01`).

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
2. Validate the YAML. Every entry must have: `id`, `intent` (non-empty string), `layout` (non-empty string).
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
Styles: 5 | Page-stories: 1 | Combinations: 5
  01-sidebar-brutalist       x page-story-ying-xiao.md
  42-col-editorial-01        x page-story-ying-xiao.md
  ...
```

Proceed only after user confirms (or immediately if running in autonomous mode).

---

## Phase 2 -- For Each Combination

For each (style entry, page-story) pair, execute Steps A through E sequentially. Derive `<id>` from the style entry's `id` field.

### Step A -- Create Design Context

This step replaces page-claw's interactive Step 1. Instead of asking questions and invoking teach-impeccable, synthesize the design context from the style entry's `intent` and `layout` fields combined with the page-story content.

Write `output/docs/<id>-design.md` with the following structure:

```markdown
# <id>

## Design Context

### Users
Infer target audience from the page-story content (academic peers, recruiters, general visitors, etc.). State the primary and secondary audiences.

### Brand Personality
Derive from the intent description and the page-story tone. State 3-5 adjectives.

### Aesthetic Direction
Restate the intent from the style matrix. Describe the overall mood and visual character it produces. Include the layout description.

### Design Principles
3-4 principles that govern this aesthetic applied to this content. Derive from the intersection of the intent and the page-story subject matter.
```

The `intent` field is the primary creative input. It describes design actions and character (e.g., "drop caps, pull quotes, mixed serif/sans pairing") without prescribing specific colors, fonts, or CSS values. Pass this intent faithfully to Step B — do not interpret it into concrete design decisions yourself.

Do not ask the user any questions. Do not invoke teach-impeccable.

**Verify:** `output/docs/<id>-design.md` exists and contains `## Design Context` with all subsections above.

### Step B -- Design System

Invoke the `ui-ux-pro-max` skill with `--design-system`. Use the page-story content and the design context from Step A (including the full intent description) as inputs.

ui-ux-pro-max is responsible for all concrete design decisions: color palette, typography pairing, spacing scale, surface treatment, decorative rules, and dark mode mapping. The intent description provides creative direction; ui-ux-pro-max translates it into a complete, executable design system.

Append the skill's output as a `## Design System` section to `output/docs/<id>-design.md`.

**Verify:** `output/docs/<id>-design.md` contains both `## Design Context` and `## Design System`.

### Step C -- Implementation Plan

Invoke the `writing-plans` skill. Use the design doc as spec.

Output: `output/docs/<id>-impl.md` containing a task-by-task build plan.

**Verify:** `output/docs/<id>-impl.md` exists and contains a task-by-task plan.

### Step D -- Build

Execute the implementation plan. The plan drives all structural and visual decisions.

Before marking the build complete, verify the Technical Contract (see Constraints) and:

- [ ] All interactive elements have `:hover` and `:focus-visible` states
- [ ] Typography hierarchy: at least 3 distinct size/weight levels
- [ ] Consistent spacing rhythm throughout
- [ ] Responsive: no horizontal scroll at 375px or 1200px
- [ ] Rendering Conventions applied (see below)
- [ ] Design intent from the style matrix is reflected in the final output
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

1. Lists every generated template with its id, intent summary, and a link to the HTML file.
2. Groups entries by page-story if multiple page-stories were used.
3. Includes its own light/dark mode toggle.
4. Uses clean, minimal styling (not one of the matrix aesthetics).

---

## Rendering Conventions

Apply the same rendering conventions defined in the `page-claw` skill:

- **Prose-to-structure mapping:** Before building, identify the semantic type of each content element (timeline, badge, relationship, etc.) and render it structurally rather than as plain text.
- **Links section:** A `## Links` section must render as icon-based links using inline SVG from Simple Icons CDN (`https://cdn.jsdelivr.net/npm/simple-icons@latest/icons/<slug>.svg`). Email uses a generic envelope SVG. Unrecognized platforms use a generic chain-link SVG.

Refer to `page-claw/SKILL.md` "Rendering Conventions" section for the full platform slug table and implementation details.

---

## Constraints

**The page-story is the authoritative source.** Render it faithfully -- do not add, remove, reorder, or reinterpret its content. The design system serves the story, not the other way around.

**Design decisions derive from the intent, layout, and page-story only.** Do not reference existing project files, other HTML pages, or prior designs unless the user explicitly requests it.

**ui-ux-pro-max owns all concrete design decisions.** Colors, fonts, spacing, surface treatment, and decorative details are determined by ui-ux-pro-max based on the intent. Do not override or second-guess its output.

**Technical Contract (applies to ALL templates):**
- All colors defined via CSS custom properties on `:root` (light) with a full `[data-theme="dark"]` override block
- Required variables in both blocks: `--bg`, `--bg-subtle`, `--fg`, `--fg-muted`, `--border`, `--accent`, `--accent-fg`
- Zero raw hex/rgb values outside `:root` and `[data-theme="dark"]` blocks
- Component CSS references colors ONLY via `var(--*)`
- Light/dark mode toggle button (sun/moon icon, top-right corner) with `localStorage` persistence and `prefers-color-scheme` as initial default

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
