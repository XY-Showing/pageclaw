# pageclaw-batch

Batch-generate static HTML page templates from a style matrix and page-story markdown files. Instead of the interactive 3-question design phase that `page-claw` uses, `pageclaw-batch` reads design intents and layout descriptions from a `style-matrix.yaml` file and lets ui-ux-pro-max make all concrete design decisions (colors, typography, spacing, decoration).

## Usage

```
/pageclaw-batch <style-matrix-path> [page-stories-glob]
```

- `<style-matrix-path>` -- path to a YAML file following the style-matrix schema.
- `[page-stories-glob]` -- optional glob for page-story files (default: `page-story-*.md` in the current directory). Each page-story is rendered once per style in the matrix.

## Input format

Each style entry requires three fields:

- `id` -- unique identifier, used as output filename (e.g., `42-col-editorial-01`)
- `intent` -- 2-5 sentences describing the aesthetic character and key design moves
- `layout` -- one-line layout skeleton description

Example entry:

```yaml
- id: "42-col-editorial-01"
  intent: >
    Mixed serif/sans pairing with strong typographic hierarchy. Drop caps,
    pull quotes, figure captions with typographic rules. Baseline grid
    alignment. Signature color (need not be neutral). Magazine-heritage
    rhythm — opener treatment differs from body sections.
  layout: "Single scrolling column, one section per content type."
```

The `intent` describes the aesthetic character and key design moves — concrete enough for ui-ux-pro-max to act on, but without prescribing specific colors, fonts, or CSS values. A complete matrix with multiple entries is at `examples/style-matrix.sample.yaml`.

## Output structure

```
output/
  html/
    <id>.html                  # One self-contained HTML file per style
  docs/
    <id>-design.md             # Design context + system doc
    <id>-impl.md               # Implementation plan
  index.html                   # Master index linking all templates
```
