Learning Conda
==============

## Contents ##

+ Managing Python Environments
+ Managing Bioinformatics Software
+ Making Recipes

### Cheat Sheet

| Task                    | Command
|:------------------------|:------------------------------------------
| create new env          | `conda create --name {env name}`
| list envs               | `conda env list`
| activate base env       | `conda activate`
| activate named env      | `conda activate {env name}`
| return to vanilla shell | `conda deactivate`
| save env as yaml        | `conda env export > env.yml`
| create env from yaml    | `conda env create -f env.yml`
| remove env              | `conda remove --name whatever`

## Assumptions ##

* You have already followed https://github.com/KorfLab/setup
* You have Anaconda/Miniconda already installed (because of the above)

## Managing Python Environments ##

Q: Yay, I have `conda` installed and I'm in the base environment. Why would I
create new environments? Why not use base all the time?

A: Most of the time, you can use the base environment. Sometimes you will run
into problems.

Fact: some versions of python incompatible with versions of python libraries.
For example, you cannot use an old version of python with a new version of
pandas. This command fails. Try it and observe the error message.

```
conda create --name broken python=3.5 pandas=1.2
```

In order to use an old version of python, you need the pandas that existed at
that time. Thankfully, you can get it.

```
conda create --name oldy python=3.5 pandas
```

This installs `pandas-0.23.4`. There were probably lots of changes (new
functions, bug fixes) from 0.23.4 to the much later 1.2. If you're stuck using
python 3.5 for some reason, you only get the old pandas, because that's the one
that works with python 3.5.

Fact: some versions of libraries are incompatible with other versions of
libraries. In order to get software to run, you may have to specify version
numbers of python and several libraries. Some people will tell you to specify
your environnments very precisely because "you know it works" as is shown here.

```
conda create --name myenv pandas=1.2 matplotlib=3.3
```

The alternative is to specify your environment loosely, and let conda figure
out the latest compatible versions (as of this writing, 2.1.1 and 3.7.2 for
these to libraries).

```
conda create --name myenv pandas= matplotlib
```

Which one should you do specific or loose? I'm in favor of defining loosely. In
other words, don't use version numbers. Use the latest versions of everything
where possible and only specify specific version numbers when there is a
conflict.

Q: Why are we using `conda` for virtual enviornments when `python3 -m venv` is
built in already?

A: Because there are other things aside from python libraries we want to
manage, like sequence aligners, peak callers, etc.


## Managing Bioinformatics Software ##


## Making Recipes