# nhsy-hcp

[![Documentation](https://github.com/nhsy-hcp/nhsy-hcp.github.io/actions/workflows/deploy.yml/badge.svg)](https://github.com/nhsy-hcp/nhsy-hcp.github.io/actions/workflows/deploy.yml)

Documentation site for example labs, built with [Zensical](https://zensical.org).

Visit [https://nhsy-hcp.github.io](https://nhsy-hcp.github.io)

## Local Development

Requires [uv](https://docs.astral.sh/uv/).

```bash
# Install dependencies into .venv
uv sync

# Serve locally on http://127.0.0.1:8000
uv run zensical serve

# Build the static site into site/
uv run zensical build --clean --strict
```

Or via [Task](https://taskfile.dev):

```bash
task init
task up
task build
```

## Configuration

Site configuration lives in `zensical.toml`. See [MIGRATION_NOTES.md](MIGRATION_NOTES.md)
for the MkDocs to Zensical migration record and known gaps.
