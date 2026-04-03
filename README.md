# data-analytics
Repo with workshop materials for "Ready, Set, Publish: Write Your First Python Package"


## Set Up Package Structure and python env

## Set Up UV

uv init --lib data-analytics --python 3.13

## Set up Python Environment

uv sync

.venv appears!

Select Python Interpreter (or always activate)

uv add pandas
uv add pytest --group dev

uv sync --group dev

# Set up testing in Cursor
uv run pytest -v


# Pre-commit
pre-commit install
(uv run pre-commit install)??
pre-commit run --all-files
(uv run pre-commit run --all-file)



# test
 “Testing: Focus on Test Explorer View” (or search for “Focus on Test View”).

# Usage of package

import pandas as pd
from data_analytics import column_statistics
stats = column_statistics(df)  # numeric columns only
# stats = column_statistics(df, numeric_only=False)


# Pypi Registry Test Account
https://test.pypi.org/account/register/

Kontoeinstellungen - connect to Github

# What you need on GitHub


Repository secret PYPI_API_TOKEN — PyPI → Account → API tokens (classic) with Upload scope for this project, or a project-scoped token.
Project on PyPI whose name matches the package you upload (here the distribution is data-analytics / data_analytics).
If PYPI_API_TOKEN is missing, the publish step will fail on auth until you add it.

Trusted publishing (no token): PyPI can be set up for OIDC from GitHub; then you can switch the last step to something like uv publish --trusted-publishing always and grant id-token: write in the publish job. The workflow is intentionally token-based so it works without extra PyPI UI setup.

Note: Every push to main that passes CI will try to upload a new dev version (0.1.0.dev1, 0.1.0.dev2, …). PyPI does not allow reusing the same version.





# what is py.typed?


What it is: Usually an empty file named py.typed inside an installable package (e.g. next to your package code under src/data_analytics/).
What it signals: “This package is meant to be checked with its inline type hints (def foo(x: int) -> str, etc.).” Tools like mypy, pyright, and PyCharm then treat those annotations as authoritative for that package.
Without it: Many checkers still ignore your package’s annotations and treat it as untyped (or only use stubs if you ship separate .pyi files).
So it doesn’t change runtime behavior; it tells type checkers and IDEs that your package participates in the typed-ecosystem convention.


# Python vs Python3
