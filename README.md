Learning Conda
==============

## Assumptions ##

+ You have already followed https://github.com/KorfLab/setup
+ You have Anaconda already installed (because of the above)

## Package Management ##

Package management is a complex topic, so it will be explained using a couple
analogies.

### Cooking Analogy ###

Programs depend on libraries. It's sort of like saying that pizza depends on
ingredients. In order to make pizza, you need ingredients like flour, salt,
olive oil, cheese, etc. Computer programs, like some recipies, can be very
picky. If the recipe calls for OO flour, can you substitute AP flour or bread
flour? If the recipe calls for 68 degree water, can you substitue boiling
water? It's hard to know until the recipe fails. Programs are the same way.
Sometimes they fail to work properly because the "chef" didn't specify the
ingredients exactly.

A "package manager" is software that ensures that your ingredients are exactly
as you specify. Suppose you have an awesome recipe for orange muffins that you
got from your grandmother. It calls for the artificial orange drink "Tang".
However, when you make the recipe it doesn't taste quite the same as you
remember. That's because the recipe was developed a long time ago, and Tang
today isn't the same as it used to be. If you want to make the original recipe,
you have to specify that "Tang" is actually "tang-1.0" and not the current
"tang-2.25". Thankfully, you can still import that from Mexico.

The recipe also calls for all-purpose flour. Your grandmother used to use Gold
Medal but you happen to have King Arthur. Do you need to try to replicate the
exact flour used at the time or will it work just fine with what you have? You
can imagine that specififying every single ingredient would be a pain. And it
is. So you just need to specify the things that have changed enough to break
the recipe.

A "package manager" specifies a "base" set of ingredients for you to cook with.
It provides you with sugar, salt, flour etc. If you need something very
specific, it will get that for you.

### Genomic Analysis Analogy ###

Imagine you're performing an RNA-seq analysis. There are 2 major steps in the
process:

1. aligning the reads to the genome
2. performing differential expression analysis

You last ran the pipeline 2 years ago and got a bunch of cool figures. However,
your PI lost some of the figures and wants you to regenerate them. So you run
the pipeline again and find that it doesn't work the same as it did. Why?

+ The genome you aligned to may have been updated
+ The alignment software may have been updated
+ The pipeline software may have been updated
+ The analysis software may have been updated

Updates are necessary to fix bugs. But those bugs may change the behavior of
the software in ways you didn't predict. It's easy to specify exactly which
genome sequence you were using as you probably saved it. But exactly what
version of Python, bowtie, Snakemake, etc? You probably didn't write those
down. Even if you did, each of those programs relies on 10-20 libraries that
you don't even know the names of.

### Functional Tests ###

There is a very complex network of interdependencies in any computational
analysis. There is no way for you to keep track of all of them. What can you
do? You can check if the analysis gets the same answer as it used to.

+ Create a test set
+ Run the software
+ Save the output

Later

+ Run the software again on the test set
+ Check if the output is the same as it was before

If the output has changed, you have to fix _something_. That may be changing
your code to make it work with the new code. Or it may mean specifiying that
your code only works with specific, out-dated versions of software.

## Conda vs. Anaconda vs. Miniconda vs. Bioconda ##

### Conda ###

Conda is a package manager. It tracks all of the software in use and all of
their dependencies.

Conda is a software installer. If you need something that isn't currently
installed, Conda will go get it for you.

Conda is an environment manager. It provides you a "base" environment with lots
of useful programs and libraries. However, if you want to customize your
environment, for example by requiring the use of out-dated software needed by
the RNAseq pipeline you wrote a while back, you can activate that specific
environment with a command such as:

	conda activate rnaseq2020

Conda duplicates software already in your computer. For example, Lubuntu
already comes with Python installed at `/usr/bin/python3`. When you activate a
conda enviornment, Python is now in `/home/yourname/anaconda/bin/python3`. Does
this sound wasteful? It is. So why do we do it? So we can exactly replicate an
environment at a later date. This is a more important problem to solve than a
little space in the filesystem.

### Anaconda ###

Anaconda is a pre-packaged version of Conda with a whole bunch of data analysis
software already installed.

### Miniconda ###

Miniconda is a minimally-packaged version of Conda. If you're running a very
small VM, you might prefer it.

### Bioconda ###

Bioconda is a _channel_ for Conda. That is, it's a location where Conda can
find software. We install lots of programs via this channel.

## Mamba ##

The Conda package resolver is the brains that figures out which ingredients
each program requires. It isn't very efficient and sometimes it can take
forever to install new software. Whenever you see something that tells you to
`conda install...` you should instead do `mamba install...`. But first you have
to install `mamba`. Because this is something you want in every conda
environment, you should install it in the base environment.

	conda install mamba -n base -c conda-forge

## Activating / Deactivating Environments ##

To deactivate conda and reset your shell to the OS default behavior:

	conda deactivate

To activate the base environment:

	conda activate

To actviate some other environment:

	conda activate some_other_environment

## Creating New Environments ##

More needed in this section...

To create a new environment for go development:

	conda create --name godev
	conda activate godev
	mamba install -c conda-forge go

To export your environment as yaml:

	conda env export > env.yml

To create an environment from a yml:

	conda env create -f env.yml

