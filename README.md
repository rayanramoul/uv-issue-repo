# uv-issue-repo

The issue is rather simple, the same dependency lists does work if they are in a single package, but does not if the
same package is part of a workspace

The corresponding `pyproject.toml`:

```toml
[project]
name = "subexample"
version = "0.1.0"
description = "Add your description here"
readme = "README.md"
requires-python = ">=3.10"
dependencies = []


[project.optional-dependencies]
cpu = [
  "torch==2.3.0",
  "torchvision==0.18.0",
  "torchmetrics==0.11.*",
  "jax==0.4.13",
  "jaxlib==0.4.13",
]
gpu = [
  "torch==2.3.0",
  "torchvision==0.18.0",
  "torchmetrics==0.11.*",
  "jax[cuda11_local]==0.4.13; platform_system != 'Darwin'",
  "jaxlib==0.4.13+cuda11.cudnn86; platform_system != 'Darwin'",
  "nvidia-cudnn-cu11>=8.6.0; platform_system != 'Darwin'",
]
tpu = [
  "torch==2.3.0",
  "torchvision==0.18.0",
  "torchmetrics==0.11.*",
  "jax[tpu]==0.4.13; platform_system != 'Darwin'",
  "jaxlib===0.4.13; platform_system != 'Darwin'",
]

tpu-torch = [
  "torch==2.3.0",
  "torchvision==0.18.0",
  "torchmetrics==0.11.*",
  "torch_xla[tpu]==2.3.0",
]



[[tool.uv.index]]
name = "pytorch-cuda11"


url = "https://download.pytorch.org/whl/cu118"
explicit = true

[[tool.uv.index]]
name = "pytorch-cpu"
url = "https://download.pytorch.org/whl/cpu"

explicit = true


[tool.uv.sources]
torch = [
  { index = "pytorch-cuda11", extra = "gpu" },
  { index = "pytorch-cpu", extra = "cpu" },
]


torchvision = [
  { index = "pytorch-cuda11", extra = "gpu" },
  { index = "pytorch-cpu", extra = "cpu" },
]


[tool.uv]
prerelease = "allow"
find-links = [
  "https://storage.googleapis.com/jax-releases/jax_releases.html",

  "https://storage.googleapis.com/jax-releases/libtpu_releases.html",
  "https://storage.googleapis.com/jax-releases/jax_cuda_releases.html",
  "https://storage.googleapis.com/libtpu-releases/index.html",

]

conflicts = [ # so uv will resolve the groups independently
  [
    { extra = "gpu" },
    { extra = "cpu" },
    { extra = "tpu" },
    { extra = "tpu-torch" },
  ],
]


package = true 


[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"


[tool.hatch.metadata]
allow-direct-references = true


[tool.hatch.build.targets.wheel]
packages = ["src/subexample"]

```

# UV Version

```


uv 0.6.7 (029b9e1fc 2025-03-17)
```



# Environment
```
Both MacOS and Linux
```

# Reproduction 
In this repository the folder `
simple-package-working/`
 reproduces the single package version which works, and the `
workspace-not-working/`
 reproduces the one wrapped in a workspace which does not work
