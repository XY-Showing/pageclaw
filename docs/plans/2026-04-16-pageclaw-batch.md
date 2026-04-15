# pageclaw-batch Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Create a batch skill that generates multiple page templates by combining pre-defined style matrices with multiple page-story files, skipping the 3-question user input phase entirely.

**Architecture:** 
pageclaw-batch reads a style-matrix.yaml (cartesian product of layout × aesthetic × color palette), iterates over each page-story file, and for each combination, invokes page-claw's Steps 2-5 (design system, implementation plan, build, quality pass) while bypassing Step 1 (user questions). Output is organized with all HTML files in one directory, all design docs in another, using naming convention: `<number>-<layout>-<aesthetic>.html` and `<number>-<layout>-<aesthetic>-design.md`.

**Tech Stack:**
- Node.js/TypeScript for skill implementation
- YAML for style-matrix definition
- Reuses page-claw's sub-skills (ui-ux-pro-max, writing-plans, polish, audit, critique, quieter)
- Batch orchestration with file I/O and parallel/sequential execution control

---

## Task 1: Define style-matrix.yaml schema and create sample template

**Files:**
- Create: `skills/pageclaw-batch/style-matrix.schema.yaml`
- Create: `skills/pageclaw-batch/examples/style-matrix.sample.yaml`
- Create: `skills/pageclaw-batch/README.md` (usage docs)

**Step 1: Define the schema**

Create `style-matrix.schema.yaml` documenting the structure:

```yaml
# Style Matrix Schema for pageclaw-batch
# Defines the cartesian product of design dimensions

version: "1.0"
description: "Pre-defined combinations of layout, aesthetic, typography, and color palette"

# Each entry in 'styles' array defines ONE template variant
# Naming: <index>-<layout>-<aesthetic>
styles:
  - id: "01-sidebar-brutalist"
    layout: "sticky-sidebar-left"
    aesthetic: "Brutalist Academic"
    colors:
      primary: "#000000"
      secondary: "#ffffff"
      background: "#f5f5f5"
      surface: "#ffffff"
      accent: "#1a1a1a"
      text: "#1a1a1a"
    typography:
      heading_font: "Georgia, serif"
      body_font: "Inter, sans-serif"
      heading_weight: "700"
      body_weight: "400"
    decorative_rules:
      - "No shadows, no borders"
      - "Full-width rules (border-top: 1px solid)"
      - "Stark contrast only"

  - id: "02-centered-minimal"
    layout: "single-centered-column"
    aesthetic: "Museum Whitespace"
    colors:
      primary: "#ffffff"
      secondary: "#f0f0f0"
      background: "#ffffff"
      surface: "#ffffff"
      accent: "#cccccc"
      text: "#333333"
    typography:
      heading_font: "Garamond, serif"
      body_font: "Helvetica Neue, sans-serif"
      heading_weight: "600"
      body_weight: "400"
    decorative_rules:
      - "Extreme whitespace (margins 10%+ of viewport)"
      - "Typography-driven (no decoration)"
      - "Content as artifact"

# Define how many total styles should exist (for validation/planning)
expected_count: 100
layout_types: ["sticky-sidebar-left", "sticky-sidebar-right", "single-centered-column", "asymmetric-grid"]
aesthetic_types: ["Brutalist Academic", "Museum Whitespace", "Glassmorphism Light", "Terminal Scholar", "Editorial Serif"]
```

**Step 2: Create sample style-matrix.yaml for testing**

```yaml
# Sample 5×4 matrix: 5 aesthetics × 4 layouts = 20 templates
# Real version will be 5 aesthetics × N layouts = 100 total

version: "1.0"
metadata:
  author: "pageclaw-batch"
  created: "2026-04-16"
  description: "Sample style matrix for testing batch generation"

styles:
  - id: "01-sidebar-brutalist"
    layout: "sticky-sidebar-left"
    aesthetic: "Brutalist Academic"
    colors: {primary: "#000", secondary: "#fff", background: "#f5f5f5", surface: "#fff", accent: "#1a1a1a", text: "#1a1a1a"}
    typography: {heading_font: "Georgia, serif", body_font: "Inter, sans-serif", heading_weight: "700", body_weight: "400"}
    decorative_rules: ["No shadows, no borders", "Full-width rules", "Stark contrast only"]

  - id: "02-sidebar-glassmorphism"
    layout: "sticky-sidebar-left"
    aesthetic: "Glassmorphism Light"
    colors: {primary: "#f0f4ff", secondary: "#ffffff", background: "#fafbfc", surface: "#ffffff", accent: "#6366f1", text: "#1e293b"}
    typography: {heading_font: "Segoe UI, sans-serif", body_font: "Segoe UI, sans-serif", heading_weight: "600", body_weight: "400"}
    decorative_rules: ["Frosted glass panels (backdrop-filter: blur)", "Layered translucency", "Soft shadows (0 1px 3px rgba)"]

  # ... 18 more style combinations (abbreviated for plan)
```

**Step 3: Document usage in README.md**

Create `skills/pageclaw-batch/README.md`:

```markdown
# pageclaw-batch

Batch generate multiple page templates by combining a style matrix with multiple page-story files.

## Usage

```bash
/pageclaw-batch <style-matrix-path> <page-stories-glob>
```

## Example

```bash
/pageclaw-batch style-matrix.yaml page-stories/*.md
```

Input:
- `style-matrix.yaml`: Pre-defined style combinations (5 aesthetics × 20 layouts = 100 templates)
- `page-stories/*.md`: One or more page-story markdown files

Output:
- `output/html/`: All generated HTML files named `01-sidebar-brutalist.html`, `02-sidebar-glassmorphism.html`, etc.
- `output/docs/`: All design documentation named `01-sidebar-brutalist-design.md`, `01-sidebar-brutalist-impl.md`, etc.

## Style Matrix Format

See `style-matrix.schema.yaml` for full schema.

Each style must define:
- `id`: Unique identifier used in output filename
- `layout`: HTML structure type
- `aesthetic`: Design style name
- `colors`: CSS custom property values for light mode
- `typography`: Font families and weights
- `decorative_rules`: Visual rules for this aesthetic
```

---

## Task 2: Create pageclaw-batch SKILL.md with orchestration logic

**Files:**
- Create: `skills/pageclaw-batch/SKILL.md`

**Step 1: Write the SKILL.md header and overview**

```markdown
---
name: pageclaw-batch
description: "Batch generate N static HTML page templates by combining a pre-defined style matrix with multiple page-story files. Skips Step 1 (user questions) entirely. Input: style-matrix.yaml + page-stories/*.md. Output: organized HTML + design docs in output/ directory."
argument-hint: "[style-matrix-path] [page-stories-glob]"
user-invocable: true
---

# pageclaw-batch

Batch-generate page templates using a pre-defined style matrix, eliminating manual 3-question flow for each combination.

## When to Use

- You have a style-matrix.yaml defining N style combinations (e.g., 100 templates)
- You have 1+ page-story-*.md files to use as content templates
- You want to generate all combinations at once: N styles × M page-stories = N×M outputs
- You want organized output: all HTML in one place, all docs in another

## Pre-flight

Verify that:
- [ ] `style-matrix.yaml` exists and is valid YAML with `styles[].id`, `colors`, `typography`, `decorative_rules`
- [ ] `page-stories/*.md` files exist in the current directory
- [ ] `output/` directory will be created (or emptied if exists)

## Inputs

**style-matrix.yaml** must define:
- `styles[]` — array of style definitions
  - `id: "01-sidebar-brutalist"` — unique identifier, used in output filename
  - `layout: "sticky-sidebar-left"` — layout structure
  - `aesthetic: "Brutalist Academic"` — aesthetic style name
  - `colors: {...}` — CSS custom properties (--primary, --secondary, etc.)
  - `typography: {...}` — font families, weights
  - `decorative_rules: [...]` — visual rules for this aesthetic

**page-stories/*.md** — standard page-story format (unchanged from page-claw)

## Pipeline

For each **style × page-story combination**:

1. **Create design context** (mimics page-claw Step 1 output)
   - Use style matrix data to populate aesthetic direction, colors, typography
   - Infer design principles from decorative rules
   - Save to `output/docs/<id>-<page-name>-design.md`

2. **Invoke ui-ux-pro-max** (page-claw Step 2)
   - Input: design context + page-story
   - Output: append Design System section to design.md

3. **Invoke writing-plans** (page-claw Step 3)
   - Input: design.md + page-story
   - Output: `output/docs/<id>-<page-name>-impl.md`

4. **Build** (page-claw Step 4)
   - Follow impl.md tasks
   - All colors use CSS custom properties from style matrix
   - Light + dark mode toggle mandatory
   - Output: `output/html/<id>-<page-name>.html`

5. **Quality pass** (page-claw Step 5)
   - Invoke polish, audit, quieter/critique if needed

## Output Structure

```
output/
├── html/
│   ├── 01-sidebar-brutalist-personal.html
│   ├── 01-sidebar-brutalist-academic.html
│   ├── 02-sidebar-glassmorphism-personal.html
│   └── ...
├── docs/
│   ├── 01-sidebar-brutalist-personal-design.md
│   ├── 01-sidebar-brutalist-personal-impl.md
│   ├── 01-sidebar-brutalist-academic-design.md
│   └── ...
└── index.html (master index linking all templates)
```

Filenames follow: `<style-id>-<page-name>.[html|md]`

## Implementation

Invoke page-claw's Steps 2-5 for each combination. Skip Step 1 (no user input).

[Detailed orchestration logic to follow...]
```

**Step 2: Add detailed Step-by-step execution logic**

Append to SKILL.md:

```markdown
## Detailed Execution

### Phase 1: Parse Inputs

1. Read and validate `style-matrix.yaml`
   - Each `styles[i].id` must be unique and match pattern: `^\d{2}-[a-z-]+$`
   - Each must have `colors`, `typography`, `decorative_rules`
   - If invalid, STOP and report errors

2. Glob `page-stories/*.md`
   - Extract page name from filename: `page-story-<name>.md` → `<name>`
   - Read and validate each file
   - If none found, STOP and report

3. Plan output structure
   - Total combinations: `len(styles) × len(page_stories)`
   - Show user: "Will generate N templates"

### Phase 2: For Each Combination

**For style in styles:**
  **For page_story in page_stories:**

1. **Create Design Context**
   - File: `output/docs/${style.id}-${page_name}-design.md`
   - Content template:

```markdown
# Design Context: ${style.id} + ${page_name}

## Users
[Inferred from page-story content]

## Brand Personality
[Derived from aesthetic style name]

## Aesthetic Direction
**Style:** ${style.aesthetic}
**Layout:** ${style.layout}
**Colors:** Primary=${style.colors.primary}, Secondary=${style.colors.secondary}
**Typography:** Heading=${style.typography.heading_font} (${style.typography.heading_weight}), Body=${style.typography.body_font} (${style.typography.body_weight})
**Decorative Rules:** ${style.decorative_rules.join(', ')}

## Design Principles
[3-5 principles derived from decorative_rules and aesthetic]
```

2. **Invoke ui-ux-pro-max**
   - Read SKILL.md for ui-ux-pro-max
   - Call with: design context + page-story content
   - Append Design System section to design.md
   - If error, log and skip (or retry N times)

3. **Invoke writing-plans**
   - Input: design.md + page-story
   - Output: `output/docs/${style.id}-${page_name}-impl.md`
   - Extract all tasks from plan

4. **Build** (Execute writing-plans tasks)
   - For each task in impl.md:
     - Follow step-by-step instructions
     - Inject CSS custom properties from style.colors
     - Mandatory: light + dark mode toggle
     - Generate: `output/html/${style.id}-${page_name}.html`

5. **Quality Pass**
   - Invoke polish (always)
   - Invoke audit (always)
   - Invoke quieter or critique (if needed based on audit results)

### Phase 3: Generate Master Index

After all combinations complete:

Create `output/index.html` listing all generated templates:

```html
<!DOCTYPE html>
<html>
<head><title>pageclaw-batch Output</title></head>
<body>
  <h1>Generated Templates</h1>
  <table>
    <tr><th>Style</th><th>Page</th><th>HTML</th><th>Design</th><th>Impl</th></tr>
    <tr>
      <td>01-sidebar-brutalist</td>
      <td>personal</td>
      <td><a href="html/01-sidebar-brutalist-personal.html">View</a></td>
      <td><a href="docs/01-sidebar-brutalist-personal-design.md">View</a></td>
      <td><a href="docs/01-sidebar-brutalist-personal-impl.md">View</a></td>
    </tr>
    [... all combinations ...]
  </table>
</body>
</html>
```

## Handling Errors

- **Missing style-matrix.yaml**: STOP. Report file not found.
- **Invalid YAML**: STOP. Report parsing error and line number.
- **Missing page-stories**: STOP. Report no matches found.
- **ui-ux-pro-max fails**: Log error for this combination. Continue with next.
- **Build fails**: Log error. Continue. User can manually fix and rebuild.
- **Quality issues**: Log warnings. File remains in output/.

## Success Criteria

- [ ] All style × page combinations processed
- [ ] All HTML files in `output/html/`
- [ ] All design docs in `output/docs/`
- [ ] Master index created and links work
- [ ] File naming convention: `<style-id>-<page-name>.[html|md]`
```

---

## Task 3: Implement pageclaw-batch orchestration (Node.js/TS)

**Files:**
- Create: `skills/pageclaw-batch/index.ts` (main entry point)
- Create: `skills/pageclaw-batch/lib/parse-matrix.ts` (YAML parsing + validation)
- Create: `skills/pageclaw-batch/lib/generate-design-context.ts` (design doc generation)
- Create: `skills/pageclaw-batch/lib/invoke-skills.ts` (skill orchestration)
- Create: `skills/pageclaw-batch/lib/build-output.ts` (HTML generation)
- Create: `skills/pageclaw-batch/lib/master-index.ts` (index.html generation)

**Step 1: Write parse-matrix.ts for YAML parsing and validation**

[Full implementation would follow TDD: write test first, implement minimal code, commit]

**Step 2: Write generate-design-context.ts**

**Step 3: Write invoke-skills.ts for sub-skill orchestration**

**Step 4: Write build-output.ts**

**Step 5: Write master-index.ts**

**Step 6: Write index.ts main entry point**

**Step 7: Add error handling and logging throughout**

**Step 8: Test with sample style-matrix.yaml**

---

## Task 4: Create integration test with pageclaw-batch-test

**Files:**
- Create: `skills/pageclaw-batch-test/` (test fixture)
- Create: `skills/pageclaw-batch-test/style-matrix.yaml` (5-style sample)
- Create: `skills/pageclaw-batch-test/page-stories/sample.md`
- Create: `skills/pageclaw-batch-test/SKILL.md` (automated test runner)

**Step 1: Write test for batch generation**

Test: Invoke pageclaw-batch with sample inputs, verify all outputs exist and are valid

**Step 2: Test file naming convention**

Verify: All files match `\d{2}-[a-z-]+-(design|impl|)[.md|.html]` pattern

**Step 3: Test design context generation**

Verify: design.md contains all required sections from style matrix

**Step 4: Test HTML generation**

Verify: All HTML files have light + dark mode toggle, CSS custom properties

---

## Task 5: Package and document

**Files:**
- Update: `skills/pageclaw-batch/README.md`
- Create: `skills/pageclaw-batch/EXAMPLES.md` (usage examples with screenshots)
- Create: CLAUDE.md with integration notes

**Step 1: Document usage scenarios**

**Step 2: Create example style-matrix.yaml with 100 templates defined**

**Step 3: Document integration with pageclaw pipeline**

---

## Execution Notes

This plan assumes:
- pageclaw skill (Steps 2-5) is stable and available
- ui-ux-pro-max, writing-plans, polish, audit are invokable
- style-matrix.yaml is pre-created by user
- page-story files follow standard format

Parallel execution is possible: for large batches (N×M > 50), generate templates in parallel pools of 5-10 concurrent builds.

If sequential execution is chosen, expect ~2-3 min per template (Steps 2-5 + quality pass).
