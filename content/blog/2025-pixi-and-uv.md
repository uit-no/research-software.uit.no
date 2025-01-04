+++
title = "Using pixi and uv on a supercomputer"
description = "A step-by-step guide on how to run Python code in an environment managed by Pixi or uv on a supercomputer"
date = "2025-01-04"

[extra]
authors = "Radovan Bast"
+++

<!-- toc -->

<img src="/blog/2025-pixi-and-uv.jpg" alt="Using the back-to-the-future meme: Pixi and uv? Your kids are gonna love it" width="500px"/>


## Motivation

Python projects often depend on a variety of libraries and tools.  The two
standard ways to manage these dependencies are Conda environments (many tools
exist to manage those) and pip (virtual environments).

One difficulty on a supercomputer with a network file system is that installing
dependencies can be slow and can create tens of thousands of files which can be
slow to access. We need to be very careful where to write these files and where
we manage cache files.

In this blog post, I will show how to manage dependencies using
[Pixi](https://pixi.sh/) (modern take on managing Conda environments) and
[uv](https://docs.astral.sh/uv/) (modern take on managing virtual
environments). **These tools are modern, open source, cross-platform, super
fast, and designed for ergonomic use and reproducibility.** I believe that it
is not too early to start using them.

Reproducibility is what we want and therefore we will **define our environments
using files** (`environment.yml` for Pixi and `requirements.txt` for uv).


## Installing a Conda environment with Pixi

My starting point are three files:
```
.
├── environment.yml
├── example.py
└── run.sh

0 directories, 3 files
```

My example `environment.yml` file happens to contain the following:
```yaml
name: course
channels:
  - conda-forge
dependencies:
  - python <= 3.12
  - jupyterlab
  - altair-all
  - vega_datasets
  - pandas
  - numpy
  - scalene
  - ruff
  - icecream
```

We don't need to really see the content of `example.py`. It is the file that I
wish to run in the environment defined by `environment.yml`.

The `run.sh` file is the interesting part:
```bash
#!/usr/bin/env bash

#SBATCH --account=nn9997k
#SBATCH --job-name='test'
#SBATCH --time=0-00:10:00
#SBATCH --mem-per-cpu=2G
#SBATCH --ntasks=1

# how to adapt this for your project:
# - change the account (above)
# - check and adjust PIXI_CACHE_DIR (below)
# - place this script in your project folder, not in your home

# use safe bash settings
set -euf -o pipefail

PIXI_VERSION="v0.39.4"

# we have to re-define the cache directory otherwise it writes files to
# ~/.cache and fills home quota
# it would make sense to use one cache for all projects or your entire research
# group but don't put it in your home
# just for the sake of demonstration it is now placed in ${USERWORK} but
# project space would be better
export PIXI_CACHE_DIR=${USERWORK}/cache

# if pixi does not exist, download it
if [ ! -e pixi ]; then
    wget -qO- https://github.com/prefix-dev/pixi/releases/download/${PIXI_VERSION}/pixi-x86_64-unknown-linux-musl.tar.gz | tar -xz
fi

# create pixi.toml from environment.yml
if [ ! -e pixi.toml ]; then
    ./pixi init --import environment.yml
fi

# finally here is your example code running inside the environment
./pixi run python example.py
```

I send it to the queue with `sbatch run.sh`. First time I run this script, it
will download `pixi`, create a `pixi.toml` file from `environment.yml`, and
install dependencies.

The second time I run this script, it will not need to re-install dependencies
and the run will take a minute less. In other words it is not a
problem to have the installation as part of the job but you can also split this
into two scripts: one for installation and one for running the code.

This is how my folder looks after the run:
```
.
├── environment.yml
├── example.py
├── .gitattributes
├── .gitignore
├── .pixi
├── pixi
├── pixi.lock
├── pixi.toml
├── run.sh
└── slurm-13566209.out

1 directory, 9 files
```

The `.pixi` folder contains the environment. The environment is defined by
`pixi.toml` and the lock file is `pixi.lock`.


## Creating a virtual environment using uv

Like in the previous example, I start with three files:
```
.
├── example.py
├── requirements.txt
└── run.sh

0 directories, 3 files
```

This time I will install dependencies defined in the file `requirements.txt`
which happens to contain the following:
```
numpy
matplotlib
pandas
```

The interesting part is again the `run.sh` file:
```bash
#!/usr/bin/env bash

#SBATCH --account=nn9997k
#SBATCH --job-name='test'
#SBATCH --time=0-00:10:00
#SBATCH --mem-per-cpu=2G
#SBATCH --ntasks=1

# how to adapt this for your project:
# - change the account (above)
# - check and adjust UV_CACHE_DIR (below)
# - place this script in your project folder, not in your home

# use safe bash settings
set -euf -o pipefail

UV_VERSION="0.5.14"

# we have to re-define the cache directory otherwise it writes files to
# ~/.cache and fills home quota
# it would make sense to use one cache for all projects or your entire research
# group but don't put it in your home
# just for the sake of demonstration it is now placed in ${USERWORK} but
# project space would be better
export UV_CACHE_DIR=/cluster/work/users/bast/experiment/cache

# if uv does not exist, download it
if [ ! -e uv ]; then
    wget -q https://github.com/astral-sh/uv/releases/download/${UV_VERSION}/uv-x86_64-unknown-linux-musl.tar.gz -O - | tar xz --strip-components=1 -C . uv-x86_64-unknown-linux-musl/uv
fi

# create a fresh virtual environment unless it exists
if [ ! -d venv ]; then
    ./uv venv venv
fi

# activate the environment
source venv/bin/activate

# synchronize environment with requirements.txt
# in other words: install dependencies into the venv
./uv pip sync requirements.txt

# run the example code
./uv run python example.py
```

I send it to the queue with `sbatch run.sh`. First time I run this script, it
will download `uv`, create the virtual environment `venv`, and install
dependencies defined in `requirements.txt`.

The second time I run this script, it will not need to re-install dependencies
and the run will take a minute less. In other words it is not a
problem to have the installation as part of the job but you can also split this
into two scripts: one for installation and one for running the code.

This is how my folder looks after the run:
```
.
├── example.py
├── requirements.txt
├── run.sh
├── slurm-13566280.out
├── uv
└── venv

1 directory, 5 files
```

The `venv` folder contains the environment. It is good that the environment is close to the calculation
since both belong together to make the calculation reproducible.
