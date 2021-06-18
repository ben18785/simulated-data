---
author: Ben Lambert
title: Practices for reproducible data analysis
date: 23 June 2021
---
# Lecture content

- How to write unsmelly code
- How to programme defensively for research
- How to structure your project for reproducible research

# What this lecture is not

It's not about writing good software; it's some tips to help your research be more reproducible.

(Although there is some overlap.)

# Code smells

# Empathy and writing

* Steven Pinker argues that empathy is key to good academic writing
* Even if writing solo, you can reduce the empathy gap with readers
* You can ask a future you if they understand what you mean
* (Better) you can ask others if they understand

# Empathy and coding

* For others to be able to reproduce or replicate what you've done, they have to understand it
* Writing understandable code also makes it less likely to be buggy
* Empathy with future users is hence key to writing good code

# Empathy and coding

Reduce the empathy gap by:

* Refactoring relentlessly as future you re-encounters code
* Code collaboratively
* Being aware of good and bad coding practices

# Good and bad code

There are many ways to write good software.

There are fewer ways to write bad software.

*Code smells* are recognised patterns of code likely to hinder understanding and make it hard to work with.

Code smells are actually named (Fowler, 2000).

# Which is not a real code smell?

* Long Parameter List
* Duplicated Code
* Global Data
* Large Class
* Feature Envy
* Inappropriate Intimacy
* Angry Lovers
* Freeloader

# Why learn how to smell?

Code smells have well-defined remedies.

Learning to classify code as whiffing of a particular odour means you can apply these.

# What is this function about ?

```R
model <- function(start, stop, stoc, spec, dens,
                  b, i_mat, i_dur, ntype, ncov)
{
  # function's body
}
```

# Change names to convey meaning

```R
simulate <- function(time_start, time_stop, is_stochastic,
                     mosquito_species, mosquito_density,
                     mosquito_to_human_prob, immunity_maternal,
                     immunity_duration, net_type, net_coverage)
{
  # function's body
}
```

# Tip # 1

"Have you met my good friend 'bSty_1800'?"

​	Use descriptive variable names.

# Other problems?

```R
simulate <- function(time_start, time_stop, is_stochastic,
                     mosquito_species, mosquito_density,
                     mosquito_to_human_prob, immunity_maternal,
                     immunity_duration, net_type, net_coverage)
{
  # function's body
}
```

- Parameters could be easily mixed up
- It's not clear what types things are expected to be
- If we add more parameters, things will get unwieldy!

# Making it better?

```R
SimulationParameters <- setClass(
  "SimulationParameters",
  slots=list(time_start="numeric",
             time_stop="numeric",
             is_stochastic="logical"))
```

# Making it better?

```R
# fails
simulation_parameters <- SimulationParameters(
    time_start=1990,
    time_stop=2018,
    is_stochastic=2
)

# works
simulation_parameters <- SimulationParameters(
    time_start=1990,
    time_stop=2018,
    is_stochastic=FALSE
)
```

# Validity checking

```R
check <-function(object) {

  if(!object@net_type %in% c("net 1", "net 2"))
    return("A net must of type `net 1` or `net 2`.")

  coverage <- object@net_coverage
  if(coverage < 0 | coverage > 1)
    return("Net coverage must not be outside [0, 1].")
}

BednetParameters <- setClass(
  "BednetParameters",
  slots=list(net_type="character",
             net_coverage="numeric"),
  validity = check))
```

# Validity checking

```R
# fails
bednet_parameters <- BednetParameters(
  net_type = "net 3",
  net_coverage = 0.3)

# fails
bednet_parameters <- BednetParameters(
  net_type = "net 2",
  net_coverage = 50)
```

# Simulation object

```R
Simulation <- setClass(
  "Simulation",
  slots=list(simulation_parameters="SimulationParameters",
             bednet_parameters="BednetParameters",
             immunity_parameters="ImmunityParameters",
             mosquito_parameters="MosquitoParameters"))

# ...don't show how to do but could create a "run" method
simulation <- Simulation(simulation_parameters=....)
result <- run(simulation)

# what parameters did I use to run my simulation?
simulation@simulation_parameters
```

# Tip # 2

"Oh you're going the same way as me? Why not ride-share?"

​	Repackage your long parameter lists into sets of objects that travel together.

"Can you prove you're of correct type? If not, you're not getting into this function."

​	Check for valid parameter values.

# Conclusions from example

* This was an example of a "long parameter list" code smell
* It also was an example of using "non-descriptive variable names"

# Long method smell

A long method is simply one with many lines of code. "Generally, any method longer than ten lines should make you start asking questions", **Refactoring Guru**.

Why do they occur?



* It's easier to write code than it is to read it
* When new functionality is required, it can feel easier to add to existing methods rather than create new ones

# What problems do they cause?

The longer a function is

* The harder it is to understand it
* The harder it is to maintain
* The harder it is to test
* The more likely it will contain duplicate code

# Where to find out more

* *Code Smells and Feels*, Jenny Byran, useR 2018
* Martin Fowler, Kent Beck *Refactoring, 2nd edition* (Esp chapter 3)
* "Better Codehub" badges on Github: semi-automated checking of software

# How to programme defensively for research

# Defensive programming

"Assumes mistakes will happen and tries to find them as soon as they arise.''

# Data science analyses

How do we programme defensively in:

- Exploratory analyses?
- Mature analyses?
- Analyses underlying publications?

# Exploratory analyses

Characteristics:

* Scoping analysis when first get hands on data
* Includes some basic analyses: potentially some data munging; potentially some plotting
* Eventual research path not decided
* May sit in a Jupyter / Markdown notebook

Question: *should* we programme defensively here?

# My exploratory analysis defence

* Focus on documenting notebook (for future self)
* Graphical checks of sensibleness
* Use functions not pure script where possible
* Add assertions into code chunks

# Example assertion

```python
def sum_y_values_conditional_x(x_baseline, x, y):
  y_sum = 0
  for i in range(len(x)):
    if x[i] == x_baseline:
      y_sum += y[i]
  return y_sum

x = [1, 0, 0, 1, 0]
y = [5, 7, 3, 2, 4]

# check x = 0 sum and x = 1 sum add up to total
y_0_sum = sum_y_values_conditional_x(0, x, y)
y_1_sum = sum_y_values_conditional_x(1, x, y)
assert(y_0_sum + y_1_sum == sum(y))
```

# Maturing analyses

Characteristics:

* Works with a cleaned and munged dataset
* Building off a previous exploratory analysis
* Biggish codebase, including functions that are reused multiple times
* Unclear which analysis path will form eventual output

Question: how to code defensively here?

# My maturing analyses defence

* Assertion-driven checking of raw data processing
* Move functions out of notebooks
* Document functions
* Create unit tests for useful (lower-level) functions
  * Don't aim for 100% coverage! Useful coverage instead

# Analyses underlying publications

* Create a separate package housing methods used by the publication
* (Maybe) continuous integration testing
* Another (structured) repo containing scripts for paper

# Structuring a project for reproducibile data analyses

# Question: how should a project be structured for reproducible analyses?

# My answer

Communication!

# How to communicate an analysis

* Most published analyses are complex, involving many steps
* Only so much can be communicated in papers (or supplementary materials)
* Producing open-source repositories is key

# Repositories for data analyses

Difficulties for users:

* Understanding how to navigate your respository
* Understanding whether parts of your repository are axiomatic or produced by your analysis
* Understanding how outputs are produced

# Structuring the repository

There are are number of *opinionated* data science project templates.

The jury's still out on whether one will rule them all or whether one needs different structures for different projects.

However there are certain things that are almost always required.

# Structuring the repository

- ReadMe
- data
  - raw
  - processed
- src
- requirements
- notebooks or vignettes

# Aim for a DAG

![From "The Turing Way".](make-research-dag.png)

# DAGs through Makefiles

Makefiles:

* Build targets using recipes
* Targets have prerequisites
* Make determines what to build based on dependency tree of targets
* Uses modification time of prerequisites to update targets only when needed

# Example Makefile

```makefile
output/figure_1.png: data/input_file_1.csv scripts/generate_histogram.py
	python scripts/generate_histogram.py -i data/input_file_1.csv -o output/figure_1.png

output/figure_2.png: data/input_file_2.csv scripts/generate_histogram.py
	python scripts/generate_histogram.py -i data/input_file_2.csv -o output/figure_2.png

output/report.pdf: report/report.tex output/figure_1.png output/figure_2.png
	cd report/ && pdflatex report.tex && mv report.pdf ../output/report.pdf
```

# Defensive coding for data science

* Jes Ford's YouTube lecture, "Getting started testing in data science - PyCon 2019"
* Hanna Torrence's YouTube lecture, "Unit testing for data scientists"

# Project templates

* Cookiecutter for Python: https://drivendata.github.io/cookiecutter-data-science/
* Project Template for R: http://projecttemplate.net/index.html

# Makefiles

* The Turing Way's "Reproducibility with Make": https://the-turing-way.netlify.app/reproducible-research/make.html

# Questions?

