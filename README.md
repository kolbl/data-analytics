# data-analytics

Workshop materials for **“Ready, Set, Publish: Write Your First Python Package.”** You publish this package to a [pypiserver](https://pypi.org/project/pypiserver/) index on your laptop, then install it from another project. [kolbl/streamlit-app](https://github.com/kolbl/streamlit-app) is a Streamlit dashboard that uses the code from this repo.

---

## Development setup

### Docker (dev container)

Cursor and VS Code build `.devcontainer/` with Docker Engine on the host. Install Engine from Docker’s apt repository on Ubuntu 22.04, 24.04, or 26.04:

```bash
sudo apt update
sudo apt install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF

sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo docker run hello-world
```

To run `docker` without `sudo`, add your user to the `docker` group and open a new terminal:

```bash
sudo usermod -aG docker $USER
```

On Ubuntu in WSL2, systemd has to be running before Engine can start. In `/etc/wsl.conf`:

```ini
[boot]
systemd=true
```

Run `wsl --shutdown`, open Ubuntu again, then install Engine.

In VS Code or Cursor, install the **Dev Containers** extension (`ms-vscode-remote.remote-containers`). Open this folder, then from the Command Palette run **Dev Containers: Reopen in Container**.

### UV

Install uv (pinned installer example):

```bash
curl -LsSf https://astral.sh/uv/0.11.1/install.sh | sh -s
uv --version
```

#### Windows installers

The installer puts `uv` under `%USERPROFILE%\.local\bin`. If new terminals still do not find `uv`, persist that directory on your **User** `Path` (PowerShell):

```powershell
# Persist for your user account; run in PowerShell:
$uvBin = Join-Path $env:USERPROFILE ".local\bin"
[Environment]::SetEnvironmentVariable(
  "Path",
  "$uvBin;" + [Environment]::GetEnvironmentVariable("Path", "User"),
  "User"
)
```

Close all terminals, open a new one, then verify:

```powershell
uv --version
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
uv run pytest
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

---

## Publish to a local index

The index is [pypiserver](https://pypi.org/project/pypiserver/). It serves a folder of wheels on this laptop.

### 1. Start the index

Leave this terminal running. The dev container already has `pypi-server`, so you can skip the install there. `-a . -P .` turns authentication off. The server listens on localhost only.

```bash
uv tool install "pypiserver"
mkdir packages
pypi-server run -i localhost -p 8080 -a . -P . packages
```

### 2. Build and publish

In another terminal:

```bash
uv build
uv publish --index local
```

`dist/` now has a wheel and a source archive. http://localhost:8080/packages/ lists both files for `data-analytics` 0.1.0. Publishing the same files again skips them.

### 3. Install it from somewhere else

```bash
uv add data-analytics --index http://localhost:8080/simple/
```

---

## GitHub Pages (MkDocs)

Enable Pages on the repository:

1. **Repository settings:** [github.com/kolbl/data-analytics/settings/pages](https://github.com/kolbl/data-analytics/settings/pages)
2. **Build and deployment → Source:** choose  **GitHub Actions**.


After Pages is enabled and permissions match your workflow, deployments should succeed.
