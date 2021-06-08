# Simulated data

## Lecture: on the benefits of simulated data

* Useful for planning studies:
  * power analyses
* Useful after having collected data to determine limits of techniques: 
  * Can I actually estimate my quantities of interest given the data to hand?
    * Simulated data typically provides a minimum test of methods: if you can't estimate things with simulated data, you probably can't with real
  * Generally helps you to understand your model and see if it is appropriate:
    * If you can't fit your model to simulated data, then perhaps you need to reduce its complexity
  * Helps to select priors without resorting to using actual data to select them
* Useful for benchmarking novel methods

 # Reproducible and useful research

## Lecture

* Important to consider project folder structure
* There are various standards for this but projects should generally contain:
  * README files
  * Raw data folder (read only)
  * Processed data
  * Scripts folder
  * Output folder
  * I like Make files as a way of automating analyses with flexibility
    * Should mention how to handle cluster runs
* Cover how to structure Github repos to make them useful for others
* The importance of writing readable code to allow others to understand what you've done: code smells course
* How to make useful methods and how to communicate their limits
  * Context of use for methods development
  * Make sure the code is fully tested: unit, functional tests possibly if code is stochastic
* How to ensure research does what you think it does:
  * assertion statements
  * unit tests: probably best handled by moving common methods to a separate package
* Barriers to reproducible research in ML in medical sciences
  * Look at paper