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

See `style-matrix.schema.yaml` for the full field reference. A working example is at `examples/style-matrix.sample.yaml`.

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
