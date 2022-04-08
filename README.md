Learning Conda
==============

## Assumptions ##

+ You have already followed https://github.com/KorfLab/setup
+ You have anaconda already installed (because of the above)

## Package Management ##

Programs depend on libraries. It's sort of like saying that pizza depends on 
ingredients. In order to make pizza, you need ingredients like flour, salt, 
olive oil, cheese, etc. Computer programs, like some recipies, can be very 
picky. If the recipe calls for OO flour, can you substitute AP flour or bread 
flour? If the recipe calls for 68 degree water, can you substitue boiling 
water? It's hard to know until the recipe fails. Programs are the same way. 
Sometimes they fail to work properly because the "chef" didn't specify the 
ingredients exactly.

A "package manager" is software that ensures that your ingredients are exactly 
as you specify. So when you go to make your killer orange muffin recipe and you 
specify "Original Tang" the grocer doesn't substitute "New Tang" because 
that's what was easily available.

When you install Linux, it comes with the latest versions of the various 
libraries. Therefore, you get "Tang 2.0" which doesn't taste the same as 
"Original Tang". So in order to get "Original Tang" you have to ship it in from 
Mexico. Package mangers are the agents that go get "Original Tang" for you so 
you can cook your killer orange muffins correctly.

## Conda vs. Anaconda vs. Miniconda ##

Conda is a package manager. When you ask it to install some software, it goes 
out and finds the exact ingredients that are known to work.

Conda is also an environment manager. Let's say that one of my recipes requires 
"New Tang" and another "Original Tang". I can't use both at the same time, but 
I can switch back and forth quickly by having two different environments, one 
where "Tang" is "New Tang" and one where "Tang" is "Original Tang". To switch 
from one to the other is simple as `conda activate newtang` or whatever I 
happened to have named the environment. When conda is running an environment, 
it will always tell you what's it's called on the command line. By default, 
this is the "base" environment. Other environments get layered on top of this.

Anaconda is a pre-packaged version of Conda with a whole bunch of data analysis 
software already installed. We use it to make sure our pantry is filled right 
from the start.

Miniconda is a pre-packaged version of Conda with the minimal software needed. 
If you're running a very small VM, you might prefer it.

## Mamba ##

The Conda package resolver is the brains that figures out which ingredients 
each program requires. It isn't very efficient and sometimes it can take 
forever to install new software. Whenever you see something that tells you to 
`conda install...` you should instead do `mamba install...`. But first you have 
to install `mamba`. Because this is something you want in every conda 
environment, you should install it in the base environment.

	conda install mamba -n base -c conda-forge

## Creating New Environments ##

stuff

