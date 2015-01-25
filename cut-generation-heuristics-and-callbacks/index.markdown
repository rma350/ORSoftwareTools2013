---
layout: page
title:  "Cut Generation, Heuristics and Callbacks: How to Solve Hard IPs"
author: Ross Anderson
---

This class is a tutorial on using callbacks, a group of advanced CPLEX features. The Traveling Salesman Problem (TSP) will be used as a running example. CPLEX will be accessed through the Java Concert Technology interface. Be sure to complete the [prerequisites and installation instructions](prerequisites/index.html) before beginning the tutorial.  [These slides](../assets/cut-generation-heuristics-and-callbacks/presentation.pdf) reviewing some Java and walk through the very beginning of the tutorial.


# Tutorial

Table of Contents:

* Getting Started
  * [Set Up Your Project](getting-started/set-up-your-project/index.html)
  * [Java and CPLEX](getting-started/java-and-cplex/index.html)
  * [Java Style for CPLEX](getting-started/java-style-for-cplex/index.html)
* TSP in CPLEX
  * [The Traveling Salesman Problem](tsp-in-cplex/the-traveling-salesman-problem/index.html)
  * [Project Organization](tsp-in-cplex/project-organization/index.html)
  * [Solver Specification](tsp-in-cplex/solver-specification/index.html)
  * [Adding Variables Objectives and Constraints](tsp-in-cplex/adding-variables-objectives-and-constraints/index.html)
* Constraint Generation
  * [A First Solution by LazyConstraintCallback](constraint-generation/a-first-solution-by-lazyconstraintcallback/index.html)
  * [Running and Profiling Performance on TSPLIB](constraint-generation/running-and-profiling-performance-on-tsplib/index.html)
  * [Polynomial Time Separation and UserCutCallback](constraint-generation/polynomial-time-separation-and-usercutcallback/index.html)
  * [Profiling and Optimizing User Cuts](constraint-generation/profiling-and-optimizing-user-cuts/index.html)
* Construction and Improvement Heuristics
  * [Advanced MIP Start and Christofides Approximation](construction-and-improvement-heuristics/advanced-mip-start-and-christofides-approximation/index.html)
  * [Generating Integer Solutions and HeuristicCallback](construction-and-improvement-heuristics/generating-integer-solutions-and-heuristiccallback/index.html)
  * [Local Search with Two-Opt](construction-and-improvement-heuristics/local-search-with-two-opt/index.html)
  * [Integrating Two-Opt with Incumbent Callback](construction-and-improvement-heuristics/integrating-two-opt-with-incumbent-callback/index.html)
* Wrap Up
  * [TSPLIB Performance](wrap-up/tsplib-performance/index.html)
  * [Conclusions](wrap-up/conclusions/index.html)
  * [References](wrap-up/references/index.html)

# Useful documentation

* Cplex 12.5: [User Guide](http://www-01.ibm.com/support/knowledgecenter/SSSA5P_12.5.1/maps/ic-homepage.html)
* Java Tutorials: [Basics](http://docs.oracle.com/javase/tutorial/), [Collections/Data Structures](http://docs.oracle.com/javase/tutorial/collections/index.html), [Java Docs](http://docs.oracle.com/javase/6/docs/api/)
* JUNG Javadoc: [Java Doc](http://jung.sourceforge.net/doc/api/index.html)
* Guava: [Wiki](https://code.google.com/p/guava-libraries/wiki/GuavaExplained?tm=6), [Java Doc](http://docs.guava-libraries.googlecode.com/git-history/release/javadoc/index.html)
