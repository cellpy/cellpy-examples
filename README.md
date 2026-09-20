<img src="cellpy-icon-bw.svg" height="80" alt="cellpy-icon">

# cellpy-examples

Formerly **`Examples`** (`cellpy/Examples` → [`cellpy/cellpy-examples`](https://github.com/cellpy/cellpy-examples)).

Notebooks and scripts that show how to load, plot, and analyse battery
cycling data with [cellpy](https://cellpy.readthedocs.io). Aimed at people
who already have some Python, but you do not need to develop cellpy itself
to use this repo.

## Quick start

You need a working [conda](https://docs.conda.io/) install (Miniconda,
Anaconda, or Mambaforge) and Python 3.13+.

From the root of this repository (cellpy 2.x — recommended):

```shell
conda env create -f environments/cellpy_v2.yml
conda activate cellpy_examples_v2
```

For the latest cellpy 1.x instead, use `environments/cellpy_v1.yml` and
activate `cellpy_examples_v1`. See [environments/README.md](environments/README.md).

To refresh an environment later (after we update the YAML):

```shell
conda env update -f environments/cellpy_v2.yml --prune
```

Then open examples under the matching top-level folder (`v1/` or `v2/`).
Some examples may download small sample data files the first time you run
them (network required).

Smoke-test the install:

```shell
python v2/scripts/load_example_data.py
```

## What's in this repo

Versioned example content lives under **`v1/`** and **`v2/`** (pick the one
that matches your conda env). Shared assets stay at the repo root.

### `v1/` / `v2/` (versioned)

Each contains:

- `jupyter-notebooks/` — classic Jupyter tutorials
- `marimo-notebooks/` — Marimo notebooks
- `scripts/` — plain Python example scripts
- `other/cellpy batch utility/` — batch processing notebook + sample raw data
- `other/cellpy project template/` — cookiecutter project template

```shell
jupyter lab
# open e.g. v2/jupyter-notebooks/ in the file browser

marimo edit v2/marimo-notebooks/
python v2/scripts/load_example_data.py
```

### Shared (repo root)

| Path | Purpose |
|------|---------|
| `example_data/` | Sample data files used by the notebooks |
| `environments/` | Conda env YAMLs for cellpy 1.x / 2.x |
| `dev/` | Maintainer helpers (local editable cellpy, etc.) |


See also [environments/README.md](environments/README.md) and
[dev/README.md](dev/README.md).

## For developers

If you prefer [uv](https://docs.astral.sh/uv/) instead of conda:

```shell
uv sync
```

That installs a released `cellpy` from PyPI (locked in `uv.lock`). No local
`cellpy` checkout is required.

### Tests

Smoke tests live under `tests/`. Each case skips unless the installed
`cellpy` major matches the example tree (`v1/` ↔ 1.x, `v2/` ↔ 2.x).

Covered:

- `v*/scripts/` — example scripts
- `v*/jupyter-notebooks/*.ipynb` — tutorial notebooks
- `v*/other/cellpy batch utility/*.ipynb` — batch notebook
- `v*/other/cellpy project template/` — cookiecutter generate smoke

With the uv env (cellpy 2.x — runs the `v2/` matrix):

```shell
uv sync --group dev
MPLBACKEND=Agg uv run pytest tests/ -q
```

For the `v1/` matrix, overlay cellpy 1.x then use `--no-sync`:

```shell
uv pip install "cellpy>=1.1.0,<2"
MPLBACKEND=Agg uv run --no-sync pytest tests/ -q
```

GitHub Actions runs both majors (see `.github/workflows/examples-tests.yml`).

### Notebooks and git

Jupyter notebooks stay as `.ipynb` so GitHub can render them. Pre-commit runs:

- [ruff](https://docs.astral.sh/ruff/) check/format on `.py` and `.ipynb`
- [nbstripout](https://github.com/kynan/nbstripout) to strip outputs / execution
  counts (cleaner diffs; run notebooks locally as usual)

One-time setup after clone:

```shell
uv sync --group dev
uv run pre-commit install
```

Run hooks on the whole tree (optional):

```shell
uv run pre-commit run --all-files
```

### Local editable cellpy

To run examples against a local editable `cellpy` clone (branch/checkout
changes show up immediately):

```shell
# default path: ../cellpy (sibling of this repo)
dev/dev_sync.sh

# or an explicit path
dev/dev_sync.sh /path/to/cellpy
# CELLPY_ROOT=/path/to/cellpy dev/dev_sync.sh
```

That syncs from the lock, then runs `uv pip install -e <cellpy>`. No
`[tool.uv.sources]` path is committed (those would break clones with a
different layout, and would rewrite `uv.lock`).

`uv run` auto-syncs and would put PyPI `cellpy` back. After `dev_sync.sh`,
either:

- `export UV_NO_SYNC=1` (script also writes a gitignored `.envrc` for direnv), or
- activate the venv: `source .venv/bin/activate`, or
- `uv run --no-sync ...`

Switch branch in the `cellpy` repo as usual — the editable install tracks that
tree. Re-run `dev/dev_sync.sh` after a plain `uv sync` / `uv add`.

Back to locked PyPI `cellpy`:

```shell
dev/dev_sync.sh --pypi
```
