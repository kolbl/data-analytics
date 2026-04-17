# data-analytics

Workshop materials for **“Ready, Set, Publish: Write Your First Python Package.”** This package is published to [TestPyPI](https://test.pypi.org/) and consumed by [kolbl/streamlit-app](https://github.com/kolbl/streamlit-app), a Streamlit dashboard that uses the code from this repo.

---

## Development setup

### UV

Install uv (pinned installer example):

```bash
curl -LsSf https://astral.sh/uv/0.11.1/install.sh | sh -s
```

Scaffold the library (example from the workshop):

```bash
uv init --lib data-analytics --python 3.13
```

### Environment and dependencies

```bash
uv sync
```

A `.venv` is created. In your editor, select that interpreter (or activate the venv if you prefer).

Add packages:

```bash
uv add pandas
uv add pytest --group dev
uv sync --group dev
```

### Tests

```bash
uv run pytest -v
```

In Cursor/VS Code: open **Testing** and use “Focus on Test Explorer View” (or search for “Focus on Test View”).

### Pre-commit

```bash
pre-commit install
pre-commit run --all-files
```

If `pre-commit` is not on your PATH, use `uv run pre-commit install` and `uv run pre-commit run --all-files`.

---

## Usage

### Column max, min, and span

`calculate_max_of_column(df, column)` is implemented as **`df[column].max()`**: pandas returns the largest value with its default **`skipna=True`**, so missing values are ignored. If every cell in the column is missing, the result is NaN.

The same idea applies to **`calculate_min_of_column`** (`.min()`) and **`calculate_span_of_column`**, which is **`max − min`** on that column (again via `.max()` and `.min()`, so NaNs are skipped consistently).

```python
import pandas as pd
from data_analytics.column_statistics import (
    calculate_max_of_column,
    calculate_min_of_column,
    calculate_span_of_column,
)

df = pd.DataFrame({"x": [1.0, 5.0, 3.0, float("nan")]})
calculate_max_of_column(df, "x")   # 5.0
calculate_min_of_column(df, "x")   # 1.0
calculate_span_of_column(df, "x")  # 4.0
```

### Full summary table per column

For mean, median, mode, max, min, and related stats:

```python
from data_analytics import column_statistics

stats = column_statistics(df)  # numeric columns only
# stats = column_statistics(df, numeric_only=False)
```

---

## Publishing (TestPyPI and GitHub)

### TestPyPI account

- Register: [test.pypi.org/account/register/](https://test.pypi.org/account/register/)
- In account settings, connect GitHub if you use that integration.

### GitHub requirements

- **Secret `PYPI_API_TOKEN`**: PyPI → Account → API tokens (classic) with upload scope for this project, or a project-scoped token.
- **Project on PyPI** whose name matches what you upload (here the distribution is `data-analytics` / import name `data_analytics`).

If `PYPI_API_TOKEN` is missing, the publish step fails until you add it.

**Trusted publishing (no token):** PyPI can use OIDC from GitHub; then you can use something like `uv publish --trusted-publishing always` and grant `id-token: write` in the publish job. This repo’s workflow may be token-based so it works without extra PyPI UI setup.

**Versioning:** Every push to `main` that passes CI may upload a new dev version (`0.1.0.dev1`, `0.1.0.dev2`, …). PyPI does not allow reusing the same version.

---

## GitHub Pages (MkDocs)

Enable Pages on the repository:

1. **Repository settings:** [github.com/kolbl/data-analytics/settings/pages](https://github.com/kolbl/data-analytics/settings/pages)
2. **Build and deployment → Source:** choose a branch (often `main` or `gh-pages`) and a source (recommended: **GitHub Actions**).
3. **Workflow permissions:** **Settings** → **Code and automation** → **Actions** → **General** → under “Workflow permissions”, ensure `GITHUB_TOKEN` can read/write as needed and that Actions may create/approve PRs if your workflow requires it.
4. **Workflow `permissions` block** should include at least:

   ```yaml
   permissions:
     contents: read
     pages: write
     id-token: write
   ```

5. **Deploy step** should use the Pages environment correctly, for example:

   ```yaml
   - name: Deploy to GitHub Pages
     uses: actions/deploy-pages@v4
     with:
       environment-url: ${{ steps.deployment.outputs.page_url }}
   ```

After Pages is enabled and permissions match your workflow, deployments should succeed.
