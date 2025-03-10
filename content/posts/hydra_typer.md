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

The way I normally structure my machine learning project is to have a `cli` program that has separate commands to run a training experiment, evaluate a dataset etc. And I use `typer` to create those commands. Each of those commands in turn will use a `config` file to get the parameters. And for configuration, my go to framework is `hydra`. Since both [Typer](https://typer.tiangolo.com/) and [Hydra](https://hydra.cc) uses the input arguments from `sys` module, we need a workaround to use them together. A workaround would be a wrong word to use. What I really mean is default way of using `hydra` and `typer` involves decorating your command function. So in order to use `hydra` and `typer` together, instead of using the `@hydra.main` decorator, one should use the composable API's of `hyper`. Below is a code snippet, that has `typer` command for training and evaluation and the configuration files for each of those are under the folder, `conf/task/training.yaml` and `conf/task/evaluate.yaml`.

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

I hope this is useful!
