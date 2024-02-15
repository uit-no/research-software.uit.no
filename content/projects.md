+++
+++

# Projects

{{ div(attributes='class="uk-card uk-card-secondary uk-card-body"') }}
Here we summarize ongoing and completed projects, small and large. If you want to
learn more about what we do check out our [blog](/blog/).
{{ enddiv() }}

{{ div(attributes='class="uk-column-1-2@l"') }}

## Speeding up a Python code from 4 weeks to below an hour

In this project we reworked a Python code (in a Jupyter Notebook) which was
originally written in MATLAB. The code was processing 18000 files containing
satellite data and this took 4 weeks. At the end of the project we could do
this in below an hour, with less code.

Let us summarize our steps:
- Files were processed in pairs but instead of going through all pairs we
  decided to focus on one pair only and re-introduce the iteration over all
  pairs at the end. This gave us better focus, shorter test and debug loop, and
  more flexibility to potentially parallelize over the pairs.
- We moved the project to GitHub to simplify collaboration and to introduce a
  code review workflow.
- We started tracking issues and ideas in GitHub issue threads instead of via email.
- We identified the computationally intensive steps.
- Most of the code consisted of merging large tables which we re-implemented using a Pandas
  inner join. This significantly simplified the code.
- We moved filtering steps to earlier in the code so that we do computations on
  filtered data instead of first computing data which we might throw away
  later.
- We abstracted repeating code into functions and moved the functions outside
  of the notebook so that they can be tested and improved independently. The
  functions got then imported into the notebook.
- We delegated data cleaning to Pandas instead of performing it "manually".
- We parallelized over data chunks which can be processed independently.


## Helping a student getting an R script to run

In this short project we helped with the dependency installation step.


## Helping two students installing Python software for a course

Two students approached us to help installing software and dependencies which
they needed to attend a computational course.  We solved this with Conda and by
adjusting wrong paths. It also required a non-trivial work-around with a
symbolic link to a library which could not be found by the software.


## Translating a legacy Visual Basic program to Python

In this ongoing project we help a researcher to translate and restructure an
old legacy VB6 code base for biological food web modelling to modern object
oriented python.  The goals of this project are to improve maintainability and
ease extensibility of the code by developing a new structure.


## Running bioinformatics workflows on data hosted on NIRD

Together with the researcher, we are evaluating different methods to run
genomic and transcriptomic workflows with large datasets with regards to ease
of use, performance and extensibility. We plan to offer the solution to other
researchers as a quick and easy way of getting started with NRIS resources.


## Solving a Python problem with regular expressions

This was a 2 hours project where we helped writing a prototype Python code
which would traverse a file tree and using a regular expression would extract a
set of variables from the file names which would later be used for data
queries.


## Porting a Fortran project to CMake

The goal of this project is to compile a code that was written 4 years ago and
to port the configuration and build setup to CMake. Here we apply "code
archaeology" and at the same time we wish to document the build process better
and to make it more portable from a very specific setup (visual studio with a
particular compiler) towards in principle any Fortran compiler and any editor
and operating system.


## Optimizing an R script which takes too much time and consumes too much memory

This project is ongoing. The R script currently fills the available memory and
times out during one particular step. Our approach is to profile the memory
demands and profile the individual steps. Once the problem is isolated and
understood, we will try to overcome the bottleneck, possibly by rewriting the
library call or even the library function(s).


## Help with setting up a Python development environment

Here we have helped a researcher who is moving their development environment
from Matlab to Python with setting up an editor, configuring it with GitHub
Copilot, installing Anaconda, and setting up a Conda environment for their
project.


## Helping out with a Python course for Bioinformatics

We have contributed one lesson to a two-weeks Python course ["Introduction to
Python in the Life
Sciences"](https://github.com/Bioinformatics-teaching-UiT/Python_2023).


## Helping out with running OpenFOAM on National HPC Resources

In this project, we're collaborating with a group from the Department of
Automation and Process Engineering to streamline their computational work.
After assessing their specific needs and the capabilities of various tools,
it was concluded that OpenFOAM was the better option for this group's work
on the national High-Performance Computing (HPC) system.

Our current focus is on guiding them in effectively utilizing OpenFOAM on the
HPC system. This includes providing them with the necessary skills to navigate
the HPC environment and optimize the capabilities of OpenFOAM within it.

The ultimate goal of this project is to empower the group to independently
conduct their production calculations using OpenFOAM on the HPC system.
We aim to enhance the efficiency of their computational work and facilitate
a more powerful research outcome within the Department of
Automation and Process Engineering.


## R Code Guidance for a Biology Student

This project involved assisting a student with their R code during our office
hours. The student, conducting biology-related research, encountered some
challenges with their code. Our task was to provide the necessary guidance
to help the student understand and resolve these issues, enabling them to
continue with their research more effectively. This is an example of how we
support individual needs, even on a one-to-one basis, during our dedicated office hours.


## Setting up GitHub Copilot for a Student

In a specific case, we assisted a student with setting up GitHub Copilot,
an AI-powered code completion tool. We provided step-by-step guidance
throughout the setup process, resolving any issues that arose. By doing so,
the student was able to utilize this cutting-edge technology to enhance their
programming efficiently.


## Building a GUI

In this project we are helping a researcher in creating a Graphical User
Interface (GUI). The researcher have an existing Python program with command
line interface but wish to make the program more accessible for users
unfamiliar with command line. Together with the researcher, we have decided on
creating the GUI using the Python library ‘PyQt’ and we are sharing our
experience with research software development with the researcher.


## Getting started with Git, best practices and Xarray.

The user is getting started with Git and we have provided support and answered
Git-related questions. We have also discussed best practices and introduced the
user to Black, Flake8, and docstring guides for cleaning up code, as well as
creating top-level functions in scripts and importing functions via symlink. In
addition, the user had challenges with Python and Xarray in their research,
which we together solved.


## Electron Density Calculations with Custom Python Scripting

A researcher needed help in writing a Python script to read electron
density data from a CHGCAR file produced by VASP software. We
provided guidance on setting up Python using an environment.yml file
and advised on the optimal structure of the code. As a result, the
researcher successfully calculated the integral of the electron
density within a specified radius around an atom.


## Unlocking Advanced Research Capabilities through Customized Software Installation

A researcher required specialized support for setting up a Python environment to run and
modify the vampyr and mrcpp software packages. We provided comprehensive guidance on
environment setup and facilitated the installation of these software packages from source.
This enabled the researcher to go beyond the limitations of the official releases, offering
them the flexibility to run and modify the codes for their specific research needs. The
collaboration enhanced the researcher's computational capabilities, paving the way for more
sophisticated and tailored research outcomes


## Using autogenerated multiple choice questions in teaching

We helped a course instructor getting started with using autogenerated multiple
choice questions in their course. The instructor was introduced to new
functionality within the Canvas learning management system, such as question
banks and quizzes. In addition we provided a hands-on tutorial on how to use
R/exams to efficiently generate a large amount of multiple choice questions and
how to upload them to Canvas question banks. 


## Help with speeding up an R script by dividing it into independent chunks

The challenge here is to compute in parallel but using random numbers. This is
not completely trivial since we need to carefully use the random number seed.
This project is on-going.


## Code to position a medical device

In this project we write code to help with device positioning for [Transcranial
magnetic
stimulation](https://en.wikipedia.org/wiki/Transcranial_magnetic_stimulation).

We do this in Python and Rust but also use containers. Soon we will write a
blog post and a paper on this.


## Organizing, speeding up, and packaging a Python code which analyses island shapes

This involves profiling and organizing the code. Soon we will work on making
the code more robust, faster, and more re-usable. Finally we will together work
on packaging the project to PyPI.


## Help with Matplotlib

This was a small project but also small things can take lots of time.


## Help with R and ggplot2 when plotting water depths

Here we sat together and studied and simplified an R Studio project for
plotting measurements along water depths.


## Moving a machine learning pipeline from the laptop to the cluster

This project involved re-organizing the Python code so that independent runs
can run concurrently. To make the project easier to move from one computer to
another, we have containerized the run. We have also added a command-line
interface to the code to make it easier to study parameter-sweeps without
editing the source code every time.

{{ enddiv() }}
