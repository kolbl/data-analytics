# uv cheatsheet

Quick reference for [uv](https://docs.astral.sh/uv/) (installer, environments, projects, tools).

## Install uv

**Standalone installer** (macOS / Linux):

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Pin a version in the URL:

```bash
curl -LsSf https://astral.sh/uv/0.11.6/install.sh | sh
```

Other methods (PyPI/pipx, Homebrew, WinGet, Docker, etc.): [Installation | uv](https://docs.astral.sh/uv/getting-started/installation/#standalone-installer).

## Update uv itself

Latest:

```bash
uv self update
```

Specific version:

```bash
uv self update 0.11.6
```

## Python interpreters

Install a managed Python (e.g. 3.13):

```bash
uv python install 3.13
```

List / find interpreters: `uv python list`, `uv python find`.

## New project: `uv init`

Creates `pyproject.toml` (and often a small layout) in the current directory or under `[PATH]`.

Common flags:

| Flag | Meaning |
|------|---------|
| `--lib` | Library package layout |
| `--app` | Application layout |
| `--package` / `--no-package` | Whether the project is buildable as a package |
| `--script` | Single-file script project |
| `-p 3.13` / `--python 3.13` | Interpreter for pins / minimum version |
| `--name <name>` | Project name |
| `--vcs git` / `none` | Initialize VCS |

Example (library, Python 3.13):

```bash
uv init --lib my-package --python 3.13
```

## Virtual environment

Create `.venv` in the project (default path is usually `.venv` when run inside a project):

```bash
uv venv
```

Custom path:

```bash
uv venv .venv
```

Note: **`vv` is a typo** — the command is **`uv venv`**.

## Dependencies and sync

Install everything from the lockfile / `pyproject.toml` into the project environment:

```bash
uv sync
```

Include optional **dependency groups** (e.g. `dev`):

```bash
uv sync --group dev
```

Add a runtime dependency:

```bash
uv add requests
```

Add a **dev** dependency (two equivalent styles):

```bash
uv add pytest --dev
uv add pytest --group dev
```

`--dev` is shorthand for the development group; `--group <name>` is for any named group in `[dependency-groups]`.

Remove a dependency:

```bash
uv remove requests
```

## Run commands in the project environment

Run a script:

```bash
uv run main.py
```

Run a console tool installed in the project (e.g. pytest):

```bash
uv run pytest
uv run pytest -v
uv run pytest tests/
```

`uv run` picks up the project’s locked deps; you do not need to activate `.venv` first (unless you prefer to).

## Lockfile and inspection

Refresh / write `uv.lock` after dependency changes (often `uv add` / `uv sync` already update it):

```bash
uv lock
```

Dependency tree:

```bash
uv tree
```

## Build and publish

Build sdist/wheel:

```bash
uv build
```

Publish to a package index (configure credentials / index URL per [uv publishing docs](https://docs.astral.sh/uv/guides/publishing/)):

```bash
uv publish
```

## One-off tools: `uvx`

Run a tool from PyPI in an ephemeral environment (like “npx for Python”).

If the **PyPI package name** differs from the **CLI name**, use `--from`:

```bash
uvx --from apache-airflow airflow standalone
```

Here `apache-airflow` is the distribution; `airflow` is the command, and `standalone` is passed to the Airflow CLI.

Generic pattern:

```bash
uvx <command> [args...]
uvx --from <distribution> <command> [args...]
```

---

## See also

- [uv documentation](https://docs.astral.sh/uv/)
- This repo’s `README.md` for workshop-specific examples (`uv init --lib`, `uv sync --group dev`, etc.).
