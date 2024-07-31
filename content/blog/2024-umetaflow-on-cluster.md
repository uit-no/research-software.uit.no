+++
title = "How we deployed UMetaFlow to a cluster"
description = "A step-by-step guide on how we installed UmetaFlow on the Saga cluster"
date = "2024-07-31"

[extra]
authors = "Radovan Bast"
+++

For a project with the [Department of Medical
Biology](https://en.uit.no/enhet/forsiden?p_dimension_id=88110) at
[UiT](https://uit.no/) we wanted to deploy
[UMetaFlow](https://github.com/biosustain/snakemake_UmetaFlow) on the Saga
cluster.

First we experimented with containerizing the installation using
Apptainer/SingularityCE.  However, we found that there was "too little" on the
container side and "too much" outside of the container and decided to install
UMetaFlow and its dependencies more directly, without a container, and by using
a Conda environment.

Below is a step-by-step guide on how we did it.

<!-- toc -->


## Cloning the repository

We first clone the [snakemake_UmetaFlow](https://github.com/biosustain/snakemake_UmetaFlow) repository.
I did this in the **project directory** on the Saga cluster (`/cluster/projects/nn____k/`):

```bash
$ git clone https://github.com/biosustain/snakemake_UmetaFlow.git
$ cd snakemake_UmetaFlow
```

All other commands are run from within this newly cloned repository.


## Installing SIRIUS

To install SIRIUS, I used the following script (called `install-sirius.sh`):
```bash
#!/usr/bin/env bash

# settings to catch errors in bash scripts
set -euf -o pipefail

wget https://github.com/sirius-ms/sirius/releases/download/v6.0.1/sirius-6.0.1-linux64.zip
unzip sirius-6.0.1-linux64.zip -d resources
rm -f sirius-6.0.1-linux64.zip
```

This script automatically places the SIRIUS binary and libraries in the
`resources` directory.


## Installing the Conda environment

Save the following file as `environment.yml`:
```yaml
channels:
  - conda-forge
  - bioconda
  - defaults
dependencies:
  - python=3.11
  - networkx
  - numpy
  - openms
  - pandas
  - pip
  - pyteomics
  - python-dateutil
  - rdkit
  - snakemake
  - pip:
    - ipython
    - openms
    - mono-require
    - pyopenms
    - ms2query
```

The next step is to create a new environment from this definition file.  This
step can take 2 hours and therefore we run it as a job on the cluster.  Save
the file as `install-environment.sh`:
```bash
#!/usr/bin/env bash

#SBATCH --account=nn____k
#SBATCH --job-name=installation
#SBATCH --time=0-08:00:00
#SBATCH --mem-per-cpu=2G
#SBATCH --ntasks=1

# settings to catch errors in bash scripts
set -euf -o pipefail

# adapt these
my_conda_storage=/cluster/projects/nn____k/conda
environment_file="environment.yml"
environment_name="umetaflow"

# typically no need to modify anything below
# using mamba here since it is potentially faster than conda
# to resolve the dependencies
module load Mamba/4.14.0-0
export CONDA_PKGS_DIRS=${my_conda_storage}/package-cache
mamba env create --prefix ${my_conda_storage}/${environment_name} --file ${environment_file}
```

Adapt `--account=nn____k` and `my_conda_storage` and then submit the job with:
```bash
$ sbatch install-environment.sh
```


## Create a file that holds the credentials for SIRIUS

Save the following file as `credentials.sh` (and replace with actual values):
```bash
export SIRIUS_EMAIL="firstname.lastname@example.org"
export SIRIUS_PASSWORD="**********"
```


## Slurm script to run the workflow

Save the following file as `run-umetaflow.sh`:
```bash
#!/usr/bin/env bash

#SBATCH --account=nn____k
#SBATCH --job-name=test
#SBATCH --time=0-01:00:00
#SBATCH --mem-per-cpu=6G
#SBATCH --ntasks=4

# settings to catch errors in bash scripts
set -euf -o pipefail

module load Miniconda3/22.11.1-1
source ${EBROOTMINICONDA3}/bin/activate

# adapt this line to match the environment created further above
conda activate /cluster/projects/nn____k/conda/umetaflow

# this sets email and password for sirius
source credentials.sh

snakemake --cores ${SLURM_NTASKS}
```

Also here you need to adapt `--account=nn____k` and the paths to the Conda
environment.

Note that in contrast to the
[snakemake_UmetaFlow](https://github.com/biosustain/snakemake_UmetaFlow)
documentation we use `snakemake --cores ${SLURM_NTASKS}` and not `snakemake
--cores ${SLURM_NTASKS} --use-conda`.  This change is important since in this
case we don't want to instruct Snakemake to install and manage the Conda
environments for us.  Instead, we have already created the Conda environment
and we want to use it.

To learn more about Snakemake, please visit <https://snakemake.github.io/>.
