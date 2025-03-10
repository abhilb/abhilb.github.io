---
title: "Using Hydra and Typer together"
date: 2023-01-29T22:24:49+01:00
draft: false
---

## Using Typer and Hydra together

### What is Typer?

[Typer](https://typer.tiangolo.com/) is a library for building CLI applications and is a wrapper on top the package [click](). Unlike click, there is no need to add decorators to the function to make the function arguments show up in help.

### What is Hydra?

[Hydra](https://hydra.cc/) is a python framework to create complex configurations in an elegant and composable manner.

### Using Typer and Hyrda in the same program

Since both [Typer](https://typer.tiangolo.com/) and [Hydra](https://hydra.cc) uses the input arguments from `sys` module, we need a workaround to use them together.

```python
##!/usr/bin/env python

import typer
import hydra
from omegaconf import OmegaConf, DictConfig

app = typer.Typer()

def get_config(task:str):
    hydra_init = hydra.initialize(version_base=None, config_path="conf")

    # Compose the config
    with hydra_init:
        # This is similar to what @hydra.main would do
        cfg = hydra.compose(config_name="config", overrides=[f"task={task}"])
        return cfg

@app.command()
def train():
    print(get_config("training"))

@app.command()
def evaluate():
    print(get_config("evaluate"))

if __name__ == "__main__":
    app()
```
