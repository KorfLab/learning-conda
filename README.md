Learning Conda
==============

## Assumptions ##

* You have already followed https://github.com/KorfLab/setup
* You have Miniconda or Anaconda already installed (because of the above)
* Your terminal has `(base)` in the prompt (because of the above)


## Contents ##

+ Glossary & Quick Reference
+ Post-install Setup
+ Managing Python Environments
+ Mac/ARM Headaches
+ Managing Bioinformatics Software
+ Sharing Environments
+ Making Recipes


## Glossary & Quick Reference ##

+ anacodna - conda, plus a lot of packages for scientific analysis
+ bioconda - a channel hosting lots of bioinformatics packages
+ channel - a place where packages are kept on the Internet
+ conda - a package management program
+ mamba - a faster version of conda
+ miniconda - conda, plus a minimal set of packages
+ package - a program or set of programs from `conda install`
+ pinning - specifying a version number of a package
+ recipe - directions for making a package (advanced users only)

| Task                              | Command Line
|:----------------------------------|:----------------------------------------
| create new environment            | `conda create --name <env name>`
| list environments                 | `conda env list`
| activate base environment         | `conda activate`
| activate named environment        | `conda activate <env name>`
| install pkg in active environment | `conda install <pkg>`
| return to vanilla shell           | `conda deactivate`
| save environment as yaml          | `conda env export > env.yml`
| create environment from yaml      | `conda env create -f env.yml`
| remove pkg from environment       | `conda remove --name <env> <pkg>`
| remove entire environment         | `conda env remove --name <env>`


## Post-install Setup ##

### mamba

It can sometimes take a long time to install packages. To speed up the process,
you can install `mamba`. This is written in a faster language (C++ vs. python)
but designed to work just like `conda`. Installing `mamba` is optional.

```
conda install -n base -c conda-forge mamba
```

### Bioconda

Most members of the lab will want to install bioinformatics software from
Bioconda at some point. Perform this one-time configuration, which helps
resolve package dependencies more quickly by telling conda where and what order
to look for packages.

```
conda config --add channels defaults
conda config --add channels bioconda
conda config --add channels conda-forge
conda config --set channel_priority strict
```

## Managing Python Environments ##

Q: Yay, I have `conda` installed and I'm in the base environment. Why would I
create new environments? Why not use base all the time?

A: The base environment isn't meant to be used. Don't install anything in your
base environment (except maybe `mamba`). Each time you install software, there
is the potential for software conflicts. You wouldn't want conflicts in your
base environment. Each new, named environment you create is a collection of
compatible software overlying the base enviornment.

---

Fact: some versions of python are incompatible with versions of python
libraries. For example, you cannot use an old version of python with a new
version of pandas. This command fails. Try it and observe the error message.

```
conda create --name broken python=3.5 pandas=1.2
```

In order to use an old version of python, you need the pandas that existed at
that time. Sometimes, you can get it.

```
conda create --name oldy python=3.5 pandas
```

This installs `pandas-0.23.4`. There were probably lots of changes (new
functions, bug fixes) from 0.23.4 to the much later 1.2. If you're stuck using
python 3.5 for some reason, you only get the old pandas, because that's the one
that works with python 3.5.

Fact: some versions of libraries are incompatible with other versions of
libraries. In order to get software to run, you may have to pin version numbers
of python and several libraries.

Some people will tell you to specify your environnments very precisely because
"you know it works" as is shown here.

```
conda create --name myenv pandas=1.2 matplotlib=3.3
```

The alternative is to specify your environment generically, and let conda
figure out the latest compatible versions.

```
conda create --name myenv pandas= matplotlib
```

As of this writing, pandas is up to 2.1.1 and matplotlib is at 3.7.2. I'm not
sure what has changed, but it makes sense to pick up the latest version at all
times. In other words, don't pin version numbers in your environments unless
necessary. Only specify version numbers when there is a conflict.

If you pin lots of version numbers, you end up installing multiple, old
versions of packages. While this will work, your conda environment will be
huge, out-of-date, functionally redundant, and needlessly complex. Going back
to the cooking analogy, you will have 5 brands of white flour, 3 brands of
yeast, 6 brands of olive oil, 12 types of cheese, etc. when really all you
needed was 1 of each.

---

Q: Why are we using `conda` for virtual enviornments when `python3 -m venv` is
built in already?

A: Because there are other things aside from python libraries we want to
manage, like sequence aligners, peak callers, etc.

## Mac/ARM Headaches ##

Unlike PCs, Apple computers have used a variety of completely different CPU
architectures over the years.

+ 1984-1994 Motorola 68K
+ 1994-2006 IBM PowerPC
+ 2006-2020 Intel x86
+ 2020- Apple Silicon (ARM, M1, M2, etc)

Programs compiled for one architecture do not work on another. This causes HUGE
compatibility problems, which Apple solves with "Rosetta" software that
silently emulates older hardware. Programs using Rosetta are slower and
sometimes buggy, but the system generally works.

In 2020, Apple started releasing computers with their M1 chip, which is based
on ARM architecture. This means that all pre-compiled software, which is what
conda provides, must be re-compiled for Apple Silicon. This will take some
time, and currently some parts of conda ecosystem (e.g. Bioconda) are not
available for Apple Silicon users.

Apparently, it is possible to install Bioconda packages on ARM Macs using the
osx-x86 installer rather than the osx-arm64 installer. I guess this means that
bioinformatics software would run in emulation via Rosetta. I need to test this
and also test running both architectures in parallel.

A related problem is working on Raspberry Pi. While conda supports
linux-aarch64 (ARM), Bioconda does not. Given that Macs are much more popular
than Pis, it may be a long time before there is Bioconda support for Pis. Just
because there isn't a conda package for some usefull program (e.g. BLAST)
doesn't mean you can't build it yourself. In the old days, we always built from
source code. You may also be able to find pre-compiled software via the OS
package managers rather than conda.


## Managing Bioinformatics Software with Bioconda ##

Q: Why manage software packages with conda when I already have package managers
in Linux (apt, snap) or MacOS (homebrew)?

A: Conda is cross-platform. All of the environments you use on one computer can
also be used on another with a completely different operating system. Linux,
Mac, and Windows can all share the same environments (mostly).

Most bioinformatics software comes from Bioconda. Bioconda is NOT a version of
conda like Anaconda and Miniconda. Bioconda is a _channel_. It's a repository
that keeps track of a bunch of bioinformatics packages.

Now let's install some software. Suppose you want to do some work with BLAST
using the old (but still good) ncbi-blast software. First, create an
environment called `blast` and then activate it.

```
conda create --name blast
conda activate blast
```

You should see `(blast)` in your prompt. Next, install the `blast-legacy`
package from the bioconda channel.

```
conda install -c bioconda blast-legacy
```

You now have a bunch of new executables in your `PATH` including `blastall` and
`formatdb`. These programs are not available in your base environment or any
other environment. You have to `conda install` the software into each
environment separately. Again, don't install in the base environment.


## Sharing Environments ##

One of the main reasons for creating environments is that they are shareable.
You can create an environment with some useful programs that work together and
other people can recreate that same enviroment very easily.

To export your blast environment as a YAML file:

```
conda env export --name blast > blast.yml
```

Another person can then get all of the software from your environment using
your YAML file.

```
conda env create -f blast.yml
```


## Making Recipes ##

Working on it...
