# pageclaw-batch

Batch-generate static HTML page templates from a style matrix and page-story markdown files. Instead of the interactive 3-question design phase that `page-claw` uses, `pageclaw-batch` reads all design decisions (layout, aesthetic, colors, typography, decorative rules) from a `style-matrix.yaml` file and produces one HTML template per style entry.

## Usage

```
/pageclaw-batch <style-matrix-path> [page-stories-glob]
```

- `<style-matrix-path>` -- path to a YAML file following the style-matrix schema.
- `[page-stories-glob]` -- optional glob for page-story files (default: `page-story-*.md` in the current directory). Each page-story is rendered once per style in the matrix.

## Input format

The style matrix is a YAML file defining one or more template variants. See `style-matrix.schema.yaml` for the full field reference. A working example with five styles is at `examples/style-matrix.sample.yaml`.

Each style entry requires: `id`, `layout`, `aesthetic`, `colors`, `typography`, and `decorative_rules`.

## Output structure

```
output/
  html/
    <id>.html                  # One self-contained HTML file per style
  docs/
    <id>-design.md             # Design context + system doc
    <id>-impl.md               # Implementation plan
```

## File naming convention

Output filenames are derived from the style `id` field, which must match the pattern `^\d{2,3}-[a-z]+-[a-z]+$`:

```
01-sidebar-brutalist.html
02-centered-minimal.html
03-topnav-editorial.html
```

The numeric prefix controls ordering. The two slug segments encode the layout shorthand and aesthetic shorthand (e.g., `sidebar-brutalist` = sticky sidebar left + Brutalist Academic).
