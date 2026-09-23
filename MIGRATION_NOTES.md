# Migration notes: MkDocs/Material to Zensical

Migrated 2026-09-23. Source: MkDocs 1.6.1 + Material for MkDocs 9.7.1.
Target: Zensical 0.0.64.

## Why

Material for MkDocs is in maintenance mode (critical fixes until 2027-05-05). Its
maintainers now develop Zensical, which reads MkDocs projects directly.

## What changed

| Before | After |
|---|---|
| `mkdocs.yml` | `zensical.toml` (native format) |
| `requirements.txt` (unpinned) | `pyproject.toml` + `uv.lock`, `zensical` pinned exactly |
| `mkdocs serve` / `mkdocs build` | `zensical serve` / `zensical build` |
| `mkdocs gh-deploy` (gh-pages branch) | GitHub Pages artifact actions |

Configuration translation notes:

- **Emoji generator paths changed.** `material.extensions.emoji.twemoji` /
  `.to_svg` became `zensical.extensions.emoji.twemoji` / `.to_svg`. This is why a
  single config file cannot serve both builders, and why `mkdocs.yml` was removed
  rather than kept alongside.
- **`theme.variant = "classic"` is set explicitly.** Zensical defaults to its newer
  `modern` variant. `classic` preserves the Material for MkDocs appearance that
  `docs/stylesheets/extra.css` is written against — it targets Material internals
  (`.md-tabs`, `.md-header`, `.md-footer`, `.md-typeset`) and the
  `data-md-color-scheme` attribute contract. Verified: the only rendered difference
  between building the original `mkdocs.yml` and this `zensical.toml` was the
  `modern` vs `classic` stylesheet and its icon set.

## Dropped: git-revision-date-localized

**Not supported by Zensical** (roadmap status: Planned). The plugin was removed and
the site now shows no created/updated timestamps anywhere.

This was a deliberate decision, not an oversight. It also resolves the unlabelled
duplicate dates that previously rendered on the home page. Revisit if and when
Zensical ships a replacement.

Note the previous CI workflow checked out without `fetch-depth: 0`, so the plugin was
already falling back to build dates in production rather than real commit dates.

## Not available: social cards

The `social` plugin is **not supported** (roadmap status: In progress). Social preview
images are instead handled with a static Open Graph image and hand-written meta tags
via a template override.

Zensical templates use **MiniJinja**, not Jinja2 — templates cannot call arbitrary
Python. `docs/overrides/` was empty before this migration, so nothing needed porting.

## CI caching deviation

The refactor brief asked for Python dependency caching in CI. Zensical's publishing
documentation explicitly advises against it ("We do not recommend using caches on CI
systems as the caching functionality will undergo revisions"). Caching is therefore
**not** enabled. Revisit once Zensical's caching story settles.

## Risks to be aware of

- **Zensical is alpha software** (`Development Status :: 3 - Alpha`, 0.0.x versioning).
  0.1.0 is targeted for around 2026-11-05. The version is pinned exactly in
  `pyproject.toml`; upgrade deliberately and re-verify the rendered output.
- **Unsupported plugins are silently ignored.** Zensical does not error on a plugin it
  does not implement — it simply produces nothing. Always verify rendered HTML, not
  just the build exit code.
- Zensical does not support `exclude_docs`, `draft_docs`, `not_in_nav`, `hooks`, or
  `gh-deploy`.

## Verified working after migration

`zensical build --strict` completes with no issues. Confirmed rendering: navigation
tabs, search index, dark-mode toggle and both palettes, admonitions, code blocks,
`.md-button` links, grid cards, FontAwesome/Material icons as inline SVG, abbreviations
via `pymdownx.snippets.auto_append`, and Mermaid custom fences.

All original URLs are preserved: `/`, `/repositories/`, `/repositories/nomad/`,
`/repositories/terraform/`, `/repositories/vault/`, `/about/`.

`sitemap.xml` is generated and contains exactly the six navigation URLs — the
orphaned stub pages under `docs/hashicorp/` and `docs/cloud/` are built but excluded
from the sitemap.

## Housekeeping done in passing

- `docs/includes/abbreviations.md` had unrelated setup prose appended after the
  abbreviation definitions. Because the file is auto-appended to every page, this was
  removed.
