# Learning Conda

## Assumptions

* You have already followed https://github.com/KorfLab/setup
* You have Anaconda already installed (because of the above)

## Package Management

Package management is a complex topic, so it will be explained using a couple analogies.

### Cooking Analogy

Programs depend on libraries. It's sort of like saying that pizza depends on ingredients. In order to make pizza, you need ingredients like flour, salt, olive oil, cheese, etc. Computer programs, like some recipies, can be very picky. If the recipe calls for OO flour, can you substitute AP flour or bread flour? If the recipe calls for 68 degree water, can you substitute boiling water? It's hard to know until the recipe fails. Programs are the same way. Sometimes they fail to work properly because the "chef" didn't specify the ingredients exactly.

A "package manager" is software that ensures that your ingredients are exactly as you specify. Suppose you have an awesome recipe for orange muffins that you got from your grandmother. It calls for the artificial orange drink "Tang". However, when you make the recipe it doesn't taste quite the same as you remember. That's because the recipe was developed a long time ago, and Tang today isn't the same as it used to be. If you want to make the original recipe, you have to specify that "Tang" is actually "tang-1.0" and not the current "tang-2.25". Thankfully, you can still import that from Mexico.

The recipe also calls for all-purpose flour. Your grandmother used to use Gold Medal but you happen to have King Arthur. Do you need to try to replicate the exact flour used at the time or will it work just fine with what you have? You can imagine that specififying every single ingredient would be a pain. And it is. So you just need to specify the things that have changed enough to break the recipe.

A "package manager" specifies a "base" set of ingredients for you to cook with. It provides you with sugar, salt, flour etc. If you need something very specific, it will get that for you.

### Genomic Analysis Analogy

Imagine you're performing an RNA-seq analysis. There are 2 major steps in the process:

1. Aligning the reads to the genome
2. Performing differential expression analysis

You last ran the pipeline 2 years ago and got a bunch of cool figures. However, your PI lost some of the figures and wants you to regenerate them. So you run the pipeline again and find that it doesn't work the same as it did. Why?

* The genome you aligned to may have been updated
* The alignment software may have been updated
* The pipeline software may have been updated
* The analysis software may have been updated

Updates are necessary to fix bugs. But those bugs may change the behavior of the software in ways you didn't predict. It's easy to specify exactly which genome sequence you were using as you probably saved it. But exactly what version of Python, bowtie, Snakemake, etc? You probably didn't write those down. Even if you did, each of those programs relies on 10-20 libraries that you don't even know the names of.


## Functional Tests

There is a very complex network of interdependencies in any computational analysis. There is no way for you to keep track of all of them. What can you do? You can check if the analysis gets the same answer as it used to.

* Create a test set
* Run the software
* Save the output

Later

* Run the software again on the test set
* Check if the output is the same as it was before

If the output has changed, you have to fix _something_. That may be changing your code to make it work with the new code. Or it may mean specifiying that your code only works with specific, out-dated versions of software.

## Conda

Software installation is difficult. It's a confusing ecosystem of operating systems (Mac OS X, many versions of Linux, Windows), and many software has many dependencies (e.g. just consider base language -- C++, Java, Python, R, and their different versions).


![isolation](https://github.com/ngs-docs/2021-GGG298/raw/latest/Week3-conda_for_software_installation/conda-isolation.png)


This leads to confusing situations where different versions of underlying software are needed to run two different programs. What if you wanted to run Macs14 and sourmash both, but one wanted 'python' to mean python2 and the other wanted 'python' to mean python3?

![versions](https://github.com/ngs-docs/2021-GGG298/raw/latest/Week3-conda_for_software_installation/versions.png)

Decoupling user-focused software from underlying operating systems is a Big Deal - imagine, otherwise you'd have to rebuild software for every OS! (This is kind of what conda does for you, actually - it's just centralized!)

Also, lot of software installation currently requires (or at least is much easier with) sysadmin privileges, which is inherently dangerous.

**Why do you need isolated software install environments? Some specific reasons:**

* Your work relies on a bunch of specific versions (perhaps old versions?)
* Working with a collaborator who really likes a particular feature!
* Experiment with new packages without messing up current workflow (reproducibility!)
* Publication ("here's what I used for software", repeatability!)
* Sometimes workflows rely on incompatible software packages! see [Titus' Twitter question](https://twitter.com/ctitusbrown/status/1218252506335080449)

Conda tries to solve all of these problems, and (in my experience) largely succeeds. That's what we'll explore today.

Conda is a solution that seems to work pretty well, and can be used by any user. Downsides are that Conda duplicates software already in your computer. For example, Lubuntu already comes with Python installed at `/usr/bin/python3`. When you activate a conda enviornment, Python is now in `/home/yourname/anaconda/bin/python3`. Does this sound wasteful? It is. So why do we do it? So we can exactly replicate an environment at a later date. This is a more important problem to solve than a little space in the filesystem.

Conda is an environment manager. It provides you a "base" environment with lots of useful programs and libraries. Conda is also a package manager and software installer. It tracks all of the software in use and all of their dependencies. Furthermore, if you need something that isn't currently installed, Conda will go get it for you.

![conda image](https://angus.readthedocs.io/en/2019/_static/conda2.png)

Note that conda emerged from the Python world but is now much broader and works for many more software packages, including R!

## Setting Up Conda on Spitfire

Conda does NOT like to be in your home environment. To fix this problem, we have to tell it where to go. In the following code, please make sure to replace the appropriate variables, represented within curly brackets `{}`, with your information. 

```
export KORFHOME=/share/korflab/home/{your_directory}
export PATH=$PATH:$KORFHOME/bin
export PATH=$PATH:/share/korflab/home/{your_directory}/.conda
export PATH=$PATH:/home/{your_directory}/.conda/envs
export PYTHONPATH=$PYTHONPATH:$KORFHOME/lib
export CONDA_ENVS_PATH=$KORFHOME/.conda
export CONDA_PKGS_DIRS=$CONDA_ENVS_PATH/pkgs
module load anaconda3
alias ls="ls -F"
alias rm="rm -i"
alias cp="cp -i"
alias mv="mv -i"

PS1='\u@\h: \W:\$ '

__conda_setup="$('/software/anaconda3/4.8.3/lssc0-linux/bin/conda' 'shell.bash' 'hook' 2> /dev/null)"
if [ $? -eq 0 ]; then
        eval "$__conda_setup"
else
        if [ -f "/software/anaconda3/4.8.3/lssc0-linux/etc/profile.d/conda.sh" ]; then
                . "/software/anaconda3/4.8.3/lssc0-linux/etc/profile.d/conda.sh"
        else
                export PATH="/software/anaconda3/4.8.3/lssc0-linux/bin:$PATH"
        fi
fi
unset __conda_setup
```


Once done, copy the code into your `.bashrc` or `.profile` (or whatever other configuration file you use on epigenerate). This file will be found in your home directory. 

## Using Conda

Now that you have it set up (hopefully) properly, we need to initialize it. Run the following, and change `.bashrc`, if needed, to whatever configuration file you use.

```
conda init
echo "PS1='\w $ '" >> .bashrc
```

This will (1) initialize Conda and (2) modify your shell settings to a cleaner prompt.

Now close & reopen your terminal or run `source ~/.bashrc` (or whatever configuration file you have, such as `.profile`).

If this gives you an error, do NOT proceed. Ask Ian for help.

## Setting Up Your Channels

To make sure all our software installs correctly, let's start by configuring software sources:

```
conda config --add channels defaults
conda config --add channels bioconda
conda config --add channels conda-forge
```

You are now ready to start creating environments. From this point forward, reference the [Conda documentation](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html) or cheat-sheet below for how to create environments, activate them, deactivate them, and more.

## Conda Cheat-Sheet

To create a new environment for go development:

```
conda create --name {environment_name}
```

To activate the base environment:

```
conda activate {environment_name}
```

To deactivate conda and reset your shell to the OS default behavior (i.e. base environment):

```
conda deactivate
```

This is an important one. When you finish an analysis, you should export the environment so someone else (including your future self) can replictate it for analysis. To export your environment as a yaml file:

```
conda env export > env.yml
```

To create an environment from a yaml:

```
conda env create -f env.yml
```

To remove an environment:

```
conda env remove --name godev
```

## Variations of Conda

### Anaconda

Anaconda is a pre-packaged version of Conda with a whole bunch of data analysis software already installed.

### Miniconda

Miniconda is a minimally-packaged version of Conda. If you're running a very small VM, you might prefer it.

### Bioconda

Bioconda is a _channel_ for Conda. That is, it's a location where Conda can find software. We install lots of programs via this channel.

### Mamba

The Conda package resolver is the brains that figures out which ingredients each program requires. It isn't very efficient and sometimes it can take forever to install new software. Whenever you see something that tells you to `conda install...` you should instead do `mamba install...`. But first you have to install `mamba`. Because this is something you want in every conda environment, you should install it in the base environment.

```
conda install mamba -n base -c conda-forge
```