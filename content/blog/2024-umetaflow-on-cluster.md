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

We then created an install script to set up the Conda environment. However,
this took hours on the network file system because of the thousands of tiny
files.  Sometimes it timed out, and sometimes it crashed with surprising
errors.

Therefore, we experimented with an approach where we set up the Conda
environment "on the fly" on the local disk of a compute node.

Below is a step-by-step guide on how we ended up using it.

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


## Conda environment definition file

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
#SBATCH --gres=localscratch:20G

# settings to catch errors in bash scripts
set -euf -o pipefail

# function to install and activate the environment "on the fly"
# requires setting --gres=localscratch (above)
# advantages: no impact on the network file system or home quota, often 100x faster install
# disadvantages: a bit wasteful in terms of network and additional 1-5 minutes for each job
install_and_activate_environment() {
    local environment_file=$1

    # install latest micromamba into local scratch
    cd ${LOCALSCRATCH}
    curl -Ls https://micro.mamba.pm/api/micromamba/linux-64/latest | tar -xvj bin/micromamba
    export MAMBA_ROOT_PREFIX=${LOCALSCRATCH}/mamba-prefix
    eval "$(${LOCALSCRATCH}/bin/micromamba shell hook -s posix)"

    # install and activate the environment
    export TMPDIR=${LOCALSCRATCH}
    time micromamba -y env create --prefix ${LOCALSCRATCH}/environment/ --file ${environment_file}
    micromamba activate ${LOCALSCRATCH}/environment/

    cd ${SLURM_SUBMIT_DIR}
}

# install and activate
install_and_activate_environment "${SLURM_SUBMIT_DIR}/environment.yml"
echo "successful conda installation!"

# this sets email and password for sirius
source credentials.sh

snakemake --cores ${SLURM_NTASKS}
```

The job script creates a Conda environment and activates it "on the fly".  This
takes only perhaps 3 or 5 minutes of the job run time. The disadvantage of this
approach is that the environment disappears after the job finishes and is
re-created at each run.

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
