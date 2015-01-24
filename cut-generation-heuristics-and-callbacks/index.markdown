---
layout: page
title:  "Cut Generation, Heuristics and Callbacks: How to Solve Hard IPs"
author: Ross Anderson
---

This class is a tutorial on using callbacks, a group of advanced CPLEX features. The Traveling Salesmen Problem (TSP) will be used as a running example. CPLEX will be accessed through the Java Concert Technology interface. Be sure to complete the [prerequisites and installation instructions](prerequisites/index.html) before beginning the tutorial.  [These slides](../assets/cut-generation-heuristics-and-callbacks/presentation.pdf) reviewing some Java and walk through the very beginning of the tutorial.


# Tutorial

Table of Contents:

* Getting Started
  * Set Up Your Project
  * Java and CPLEX
  * Java Style for CPLEX
* TSP in CPLEX
  * The Traveling Salesmen Problem
  * Project Organization
  * Solver Specification
  * Adding Variables Objectives and Constraints
* Constraint Generation
  * A First Solution by LazyConstraintCallback
  * Running and Profiling Performance on TSPLIB
  * Polynomial Time Separation and UserCutCallback
  * Profiling and Optimizing User Cuts
* Construction and Improvement Heuristics
  * Advanced MIP Start and Christofides Approximation
  * Generating Integer Solutions and HeuristicCallback
  * Local Search with Two-Opt
  * Integrating Two-Opt with Incumbent Callback
* Wrap Up
  * TSPLIB Performance
  * Conclusions
  * References

# Useful documentation

* Cplex 12.5: User Guide
* Java Tutorials: Basics, Collections/Data Structures, Java Docs
* JUNG Javadoc: Java Doc
* Guava: Wiki, Java Doc
