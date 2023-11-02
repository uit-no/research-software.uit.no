+++
title = "Building SNOWPACK in a container"
description = "A step-by-step guide on how we built it inside a SingularityCE/Apptainer container"
date = "2023-11-02"

[extra]
authors = "Radovan Bast"
+++

For a project with the [Department of Geosciences](https://en.uit.no/enhet/ig)
at [UiT](https://uit.no/) we needed to build and test
[SNOWPACK](https://snowpack.slf.ch/), an open source snowpack and ground
surface model.


## Tools

We decided to do this using a [SingularityCE](https://sylabs.io/singularity/)/[Apptainer](https://apptainer.org/) container
with the motivation:
- It encourages us to document the installation process.
- We have a baseline which we can refer to from different computers and
  operating systems.
- Later, once we decide to move computations to a cluster/supercomputer, it
  will be easier to deploy there having it already containerized.


## Building the container

We use the following container definition file and call this file `snowpack.def`:
```
Bootstrap: docker
From: ubuntu:22.04

%post
    export DEBIAN_FRONTEND=noninteractive
    apt-get update -y
    apt-get install -y wget

    wget https://gitlabext.wsl.ch/snow-models/snowpack/-/package_files/428/download -O Snowpack-3.6.0-x86_64.deb
    dpkg -i Snowpack-3.6.0-x86_64.deb

%runscript
    snowpack $@

%help
    Example usage:
    $ ./snowpack.sif -c io_res1exp.ini -e 1996-06-17T00:00
```

From the above definition file we build the image `snowpack.sif`, either using `singularity` or
`apptainer` (at the time of writing both are equivalent in functionality for
our purposes here):
```bash
singularity build --fakeroot snowpack.sif snowpack.def
```

The generated image `snowpack.sif` can now be moved to other computers that
have
[SingularityCE](https://sylabs.io/singularity/)/[Apptainer](https://apptainer.org/)
installed and run there.


## Example run

First we need to download a configuration file and two input files:
```bash
wget https://code.wsl.ch/snow-models/snowpack/-/raw/master/doc/examples/cfgfiles/io_res1exp.ini

mkdir -p input output

wget https://code.wsl.ch/snow-models/snowpack/-/raw/master/doc/examples/input/MST96.sno  -O input/MST96.sno
wget https://code.wsl.ch/snow-models/snowpack/-/raw/master/doc/examples/input/MST96.smet -O input/MST96.smet
```

With the above commands, our directory now looks like this:
```
.
├── input
│   ├── MST96.smet
│   └── MST96.sno
├── io_res1exp.ini
├── output
├── snowpack.def
└── snowpack.sif
```

Now we can run the example
```bash
./snowpack.sif -c io_res1exp.ini -e 1996-06-17T00:00
```

This generates the following output:
```
[i] [2023-11-02T10:48:57] ---> Start SNOWPACK in RESEARCH mode
[i] []                 snowpack compiled on Sep  1 2020 at 22:29:53
[i] []                 Experiment : res
[i] []                 Output dir : ./output

[i] []                 Run on meteo station MST96
[i] []                 Reading snow cover data for station MST96
[i] []                 Finished initializing station MST96
[i] []                 No file ./output/MST96_res.ini to erase
[i] [2023-11-02T10:48:57] ---> Start simulation for MST96 on 1995-11-01T00:15:00+01:00
[i] []                 End date specified by user: 1996-06-17T00:00:00+01:00
[i] []                 Integration step length: 15.000000 min
[i] [1995-11-16T00:00:00] ---> Station MST96 (1 slope(s)): advanced to 16.11.1995 00:00:00 station time
[i] [1995-12-01T00:00:00] ---> Station MST96 (1 slope(s)): advanced to 01.12.1995 00:00:00 station time
[i] [1995-12-16T00:00:00] ---> Station MST96 (1 slope(s)): advanced to 16.12.1995 00:00:00 station time
[i] [1995-12-31T00:00:00] ---> Station MST96 (1 slope(s)): advanced to 31.12.1995 00:00:00 station time
[i] [1996-01-15T00:00:00] ---> Station MST96 (1 slope(s)): advanced to 15.01.1996 00:00:00 station time
[i] [1996-01-30T00:00:00] ---> Station MST96 (1 slope(s)): advanced to 30.01.1996 00:00:00 station time
[i] [1996-02-14T00:00:00] ---> Station MST96 (1 slope(s)): advanced to 14.02.1996 00:00:00 station time
[i] [1996-02-29T00:00:00] ---> Station MST96 (1 slope(s)): advanced to 29.02.1996 00:00:00 station time
[i] [1996-03-15T00:00:00] ---> Station MST96 (1 slope(s)): advanced to 15.03.1996 00:00:00 station time
[i] [1996-03-30T00:00:00] ---> Station MST96 (1 slope(s)): advanced to 30.03.1996 00:00:00 station time
[i] [1996-04-14T00:00:00] ---> Station MST96 (1 slope(s)): advanced to 14.04.1996 00:00:00 station time
[i] [1996-04-29T00:00:00] ---> Station MST96 (1 slope(s)): advanced to 29.04.1996 00:00:00 station time
[i] [1996-05-14T00:00:00] ---> Station MST96 (1 slope(s)): advanced to 14.05.1996 00:00:00 station time
[i] [1996-05-29T00:00:00] ---> Station MST96 (1 slope(s)): advanced to 29.05.1996 00:00:00 station time
[i] [1996-06-13T00:00:00] ---> Station MST96 (1 slope(s)): advanced to 13.06.1996 00:00:00 station time
[i] []                 Total time to read meteo data : 0.304980 s
[i] []                 Runtime for station MST96: 9.455373 s

[i] []                 STARTED  running SLF RESEARCH Snowpack Model on Thu Nov  2 10:48:57 2023
                       ========================================================================
                       FINISHED running SLF RESEARCH Snowpack Model on Thu Nov  2 10:49:07 2023
```
