# data-analytics

Small utilities for exploratory work with **pandas**, starting with column-wise descriptive statistics.

## Install

```bash
uv add data-analytics
```

Or from a clone of this repository:

```bash
uv sync --group dev
```

## Quick example

```python
import pandas as pd
from data_analytics import column_statistics

df = pd.DataFrame({"a": [1.0, 2.0, 3.0], "b": [4.0, 5.0, 6.0]})
stats = column_statistics(df)
```

The returned frame is indexed by **column name**; each row summarizes that column across all rows.

## Documentation

- **[API reference](api.md)** — generated from docstrings.

## Source

[github.com/kolbl/data-analytics](https://github.com/kolbl/data-analytics)
