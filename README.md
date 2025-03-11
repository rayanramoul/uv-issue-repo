# uv-issue-repo
This repository reproduce an issue encountred when using UV workspaces, if you run:
```bash
uv sync --all-packages
```
This should update the `uv.lock` file accordingly. But if you run afterward
```bash
uv sync --all-packages --locked
# OR
uv lock --locked
```
You get the following error:
```bash
error: The lockfile at `uv.lock` needs to be updated, but `--locked` was provided. To update the lockfile, run `uv lock`.
```
But the lockfile is already updated and it seems like a bug.
From experimentations it seems like the issue comes from the presence of the following line in the root-workspace `pyproject.toml`:
```toml
[tool.uv]
find-links = [
  "https://storage.googleapis.com/libtpu-releases/index.html",
]
```

While having at the same time in the sub-package `pyproject.toml`:
```toml
[tool.uv.sources]
torch = [
  { index = "pytorch-cuda11", extra = "gpu" },
  { index = "pytorch-cpu", extra = "cpu" },
]


[[tool.uv.index]]
name = "pytorch-cuda11"
url = "https://download.pytorch.org/whl/cu118"
explicit = true

[[tool.uv.index]]
name = "pytorch-cpu"
url = "https://download.pytorch.org/whl/cpu"
explicit = true
```

Removing any of them makes the issue disappear and lock behaves as expected.
