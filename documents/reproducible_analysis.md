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



# Code smells

---

# How to programme defensively for research

# Defensive programming

``Assumes mistakes will happen and tries to find them as soon as they arise.''

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

Question: how to programme defensively here?

#My maturing analyses defence

* assertion-driven checking of raw data processing
* Move functions out of notebooks
* Document functions
* Create unit tests for useful (lower-level) functions
  * Don't aim for 100% coverage! Useful coverage instead

# Analyses underlying publications

* create a separate package housing methods used by the publication
* (maybe) continuous integration testing
* another (structured) repo containing scripts for paper

# Structuring a repository for reproducibile data analyses