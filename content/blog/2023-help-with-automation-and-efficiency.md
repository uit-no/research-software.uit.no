+++
title = "Help with automation and efficiency with an R project"
description = "Some lessons learned from working with R scripts used in arctic and marine biology research"
date = "2023-05-14"

[extra]
authors = "Radovan Bast"
+++

In this project we have worked on five issues which are very typical for many R
and Python projects and which we consider "the bread and butter" of our
research software engineering support work.
- Too much manual work is needed to process each out of many analyses
- Code repetition
- Memory bottleneck
- Code takes too long to run
- Reproducibility

Below we will summarize our steps, lessons learned, and recommendations for
follow-up steps.


## Too much manual work is needed to process each out of many analyses

The analyses have to be carried out by varying a number of parameters but for
each new variation a new script was created and manually adjusted. We have
addressed this by first moving all variables to the top of the script. A good
next step will be to introduce a command-line interface (example is below) so
that parameters can be changed without changing the code.

Another problem was that the consistency of data was previously verified
visually and not automatically. We have reformulated this as a function and now
the code can check this "by itself":
```r
library(Seurat)

# function which checks whether ident is set correctly
# correctly here means that the values of the seurat_clusters
# are all represented in the levels of the integrated_samples
check_ident <- function(seurat_object) {
  unique_levels <- unique(as.numeric(levels(integrated_samples)))
  unique_clusters <- unique(integrated_samples@meta.data$seurat_clusters)

  if (!all(unique_clusters %in% unique_levels)) {
    stop("ERROR: Ident is not correctly set up")
  }
}

# load the sample
integrated_samples <- readRDS("sample.rds")

# if ident is not set correctly, script will stop here
check_ident(integrated_samples)
```


## Code repetition

The code to create a grid of plots was repetitive:
```r
plot <- cowplot::plot_grid(
  scater::plotReducedDim(cluster_0_SCE, 'Method 1', colour_by = I(clusters$clusters[, 'k.10_cluster'])) + ggtitle('k = 10'),
  scater::plotReducedDim(cluster_0_SCE, 'Method 1', colour_by = I(clusters$clusters[, 'k.20_cluster'])) + ggtitle('k = 20'),
  scater::plotReducedDim(cluster_0_SCE, 'Method 1', colour_by = I(clusters$clusters[, 'k.30_cluster'])) + ggtitle('k = 30'),
  scater::plotReducedDim(cluster_0_SCE, 'Method 1', colour_by = I(clusters$clusters[, 'k.40_cluster'])) + ggtitle('k = 40'),
  scater::plotReducedDim(cluster_0_SCE, 'Method 1', colour_by = I(clusters$clusters[, 'k.50_cluster'])) + ggtitle('k = 50'),
  nrow = 2, ncol = 3, rel_heights = c(0.5, 0.5)
  )
plot

plot <- cowplot::plot_grid(
  scater::plotReducedDim(cluster_0_SCE, 'Method 2', colour_by = I(clusters$clusters[, 'k.10_cluster'])) + ggtitle('k = 10'),
  scater::plotReducedDim(cluster_0_SCE, 'Method 2', colour_by = I(clusters$clusters[, 'k.20_cluster'])) + ggtitle('k = 20'),
  scater::plotReducedDim(cluster_0_SCE, 'Method 2', colour_by = I(clusters$clusters[, 'k.30_cluster'])) + ggtitle('k = 30'),
  scater::plotReducedDim(cluster_0_SCE, 'Method 2', colour_by = I(clusters$clusters[, 'k.40_cluster'])) + ggtitle('k = 40'),
  scater::plotReducedDim(cluster_0_SCE, 'Method 2', colour_by = I(clusters$clusters[, 'k.50_cluster'])) + ggtitle('k = 50'),
  nrow = 2, ncol = 3, rel_heights = c(0.5, 0.5))
plot
```

We have helped rewriting this to:
```r
# explanation why a for loop does not work
# https://stackoverflow.com/questions/39799886/r-assigning-ggplot-objects-to-list-in-loop

k_values <- c(10, 20, 30, 40, 50)

plot_list = lapply(k_values, function(k)
  scater::plotReducedDim(cluster_SCE, "Method 1", colour_by = I(clusters$clusters[, paste0("k.", k, "_cluster")])) +
  ggtitle(paste0("k = ", k))
  )
plot_grid <- cowplot::plot_grid(plotlist = plot_list, nrow = 2, ncol = 3, rel_heights = c(0.5, 0.5))
plot_grid

plot_list = lapply(k_values, function(k)
  scater::plotReducedDim(cluster_SCE, "Method 2", colour_by = I(clusters$clusters[, paste0("k.", k, "_cluster")])) +
  ggtitle(paste0("k = ", k))
  )
plot_grid <- cowplot::plot_grid(plotlist = plot_list, nrow = 2, ncol = 3, rel_heights = c(0.5, 0.5))
plot_grid
```


## Memory bottleneck

One of the scripts fills up the memory (over 30 GB).  Using [memory
profiling](https://bast.github.io/R-and-Python/profiling/) we have identified
that this happens when de-duplicating records with duplicate row names.

This is currently done like this:
```r
duplicated_rows <- duplicated(rownames(data))

data <- data[!duplicated_rows, ]
```

[The dataset](https://www.repository.cam.ac.uk/handle/1810/340518) is
relatively large and the above step fills the memory, fills the swap disk and
then the script crashes.

As next steps we recommend to try [dplyr](https://dplyr.tidyverse.org/) to
filter the duplicate rows and/or to process the huge dataset not in one go but
in batches and to later recombine results.


## Code takes too long to run

One analysis script takes over a week to run and this is prohibitively long not
only on a laptop but also on a supercomputing cluster. The time consuming step
happens inside a triple-loop (this is a simplified code, not the actual code):
```r
time_consuming_function <- function(i, j, k) {
  result <- 0
  # here is some time consuming computation
  # ...
  # result <- result + ...
  # ...
  result
}

i_parameters <- c(10, 20, 30, 40, 50, 60, 70, 80, 90, 100)
j_parameters <- c(2, 4, 6, 8, 10, 12, 14, 16, 18, 20)
k_parameters <- c(5, 10, 15, 20, 25, 30, 35, 40, 45, 50)

result <- 0
num_computations <- 0

for(i in i_parameters)
{
  for(j in j_parameters)
  {
    for(k in k_parameters)
    {
      result <- result + time_consuming_function(i, j, k)
      num_computations <- num_computations + 1
    }
  }
}

cat("we performed", num_computations, "computations\n")
```

This is a very common example and one strategy is to move one or several loops
to the "outside" and to introduce a command line interface:
```r
library(optparse)

option_list <- list(
  make_option(c("--i-parameter", "-i"), type = "integer", help = "help text about the i parameter", default = 1),
  make_option(c("--j-parameter", "-j"), type = "integer", help = "help text about the j parameter", default = 1)
)

args <- parse_args(OptionParser(option_list = option_list))

time_consuming_function <- function(i, j, k) {
  result <- 0
  # here is some time consuming computation
  # ...
  # result <- result + ...
  # ...
  result
}

# iterating over i and j moved "outside"
# i_parameters <- c(10, 20, 30, 40, 50, 60, 70, 80, 90, 100)
# j_parameters <- c(2, 4, 6, 8, 10, 12, 14, 16, 18, 20)

k_parameters <- c(5, 10, 15, 20, 25, 30, 35, 40, 45, 50)

result <- 0
num_computations <- 0

for(k in k_parameters)
{
  result <- result + time_consuming_function(args$i, args$j, k)
  num_computations <- num_computations + 1
}

cat("we performed", num_computations, "computations\n")
```

Note how we have changed `i` and `j` to `args$i` and `args$j`.

By introducing a command line interface (here using the [optparse](https://github.com/trevorld/r-optparse) library)
we make it possible to set *i* and *j* from the outside:
```bash
$ Rscript example.R -i 10 -j 2
```

Now we can call the script with a specific *i,j*-pair and reduce the number of
expensive function calls from 1000 to 10.  Of course we still need to iterate
over all *i,j*-combinations somewhere so the total amount of computing to be
done did not magically reduce. However, now we can do all those calculations in
parallel if we have enough cores available, for instance using [Slurm job
arrays](https://slurm.schedmd.com/job_array.html).

We also get a help text:
```bash
$ Rscript example.R --help
```

```
Usage: example.R [options]

Options:
	-i I-PARAMETER, --i-parameter=I-PARAMETER
		help text about the i parameter

	-j J-PARAMETER, --j-parameter=J-PARAMETER
		help text about the j parameter

	-h, --help
		Show this help message and exit
```


## Reproducibility

In order to simplify sharing the examples and document dependencies we have
introduced [renv](https://rstudio.github.io/renv/articles/renv.html) into the
project.  We have also developed an [Apptainer/Singularity container for
reproducible R environments](https://github.com/bast/contain-R).

This is the Apptainer/Singularity container definition file that we have used
for testing and debugging:
```bash
Bootstrap: docker
From: satijalab/seurat:latest

%post
    apt-get update -qq
    export DEBIAN_FRONTEND=noninteractive

    # possibly not all of these are needed
    apt-get install -y libfontconfig1-dev libharfbuzz-dev libfribidi-dev libcairo2-dev libfreetype6-dev libpng-dev libtiff5-dev libjpeg-dev libgif-dev libwebp-dev libpoppler-glib-dev libpoppler-private-dev libssl-dev libcurl4-openssl-dev libxml2-dev

    R --quiet -e "options(repos = c(CRAN = 'http://cran.us.r-project.org'))"
    R --quiet -e "install.packages('BiocManager')"

    R --quiet -e "BiocManager::install(c('systemfonts'))"
    R --quiet -e "BiocManager::install(c('clustree'))"
    R --quiet -e "BiocManager::install(c('scater'))"
    R --quiet -e "BiocManager::install(c('cowplot'))"
    R --quiet -e "BiocManager::install(c('scran'))"
    R --quiet -e "BiocManager::install(c('scmap'))"
```

The container is based on the container image provided
[here](https://satijalab.org/seurat/articles/install.html).
