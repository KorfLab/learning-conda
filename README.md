Learning Conda
==============

## Contents ##

+ Managing Python Environments
+ Managing Bioinformatics Software
+ Making Recipes

### Cheat Sheet

| Task                      | Command
|:--------------------------|:------------------------------------------
| create new env            | `conda create --name {env name}`
| list envs                 | `conda env list`
| activate base env         | `conda activate`
| activate named env        | `conda activate {env name}`
| install pkg in active env | `conda install <pkg>`
| return to vanilla shell   | `conda deactivate`
| save env as yaml          | `conda env export > env.yml`
| create env from yaml      | `conda env create -f env.yml`
| remove pkg from env       | `conda remove --name <env> <pkg>`
| remove entire env         | `conda env remove --name <env>`

## Assumptions ##

* You have already followed https://github.com/KorfLab/setup
* You have Anaconda/Miniconda already installed (because of the above)

## Managing Python Environments ##

Q: Yay, I have `conda` installed and I'm in the base environment. Why would I
create new environments? Why not use base all the time?

A: The base environment isn't meant to be used. Don't install anything in your
base environment (except maybe `mamba`). Each time you install software, there
is the potential for software conflicts. You wouldn't want conflicts in your
base environment. Each new environment you create is a collection of compatible
software.

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
libraries. In order to get software to run, you may have to specify version
numbers of python and several libraries.

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

As of this writing, pandas is up to 2.1.1 and matplotlib is at 3.7.2. Which one
should you do specific or general? I'm in favor of defining generally. In other
words, don't use version numbers unless you have to. Use the latest versions of
everything where possible and only specify version numbers when there is a
conflict.

If you over-specify the version numbers, you end up installing multiple, old
versions of packages. While this will work, your conda environment will be
huge, out-of-date, functionally redundant, and needlessly complex. Going back
to the cooking analogy, you will have 5 brands of white flour, 3 brands of
yeast, 6 brands of olive oil, etc. when really all you needed was 1 of each.

Q: Why are we using `conda` for virtual enviornments when `python3 -m venv` is
built in already?

A: Because there are other things aside from python libraries we want to
manage, like sequence aligners, peak callers, etc.


## Managing Bioinformatics Software ##

Q: Why manage software packages with conda when I already have package managers
in Linux (apt, snap) or MacOS (homebrew)?

A: Conda is cross-platform. All of the environments you use on one computer can
also be used on another with a completely different operating system. Linux,
Mac, and Windows can all share the same environments (mostly).

Most bioinformatics software comes from Bioconda. Bioconda is NOT a version of
conda like Anaconda and Miniconda. Bioconda is a _channel_. It's repository
that keeps track of a bunch of bioinformatics packages.

Before you start installing software, make a few one-time configurations as
shown below.

```
conda config --add channels defaults
conda config --add channels bioconda
conda config --add channels conda-forge
conda config --set channel_priority strict
```

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

Q: What the heck is bioconda? What is a channel?


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
