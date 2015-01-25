---
layout: page
title:  "Project Organization"
author: Ross Anderson
---

# Libraries

The solver will require a variety of algorithms for known combinatorial problems and efficient data structures. These algorithms and data structures have been provided for you, either through libraries (JARs) distributed with the project, or code that has already been written for you.

The libraries distributed with the project are located in the folder _tspSolver/lib/_. We now summarize their functions:

* The file _cplex.jar_ provides a Java interface to CPLEX.
* The file _guava-14.0-rc1.jar_ contains the [Guava](https://code.google.com/p/guava-libraries/wiki/GuavaExplained?tm=6) libary. Guava provides some extra features not distributed in the base Java libraries. In particular, it gives a few extra data structures which we will use, most notably the [BiMap](http://docs.guava-libraries.googlecode.com/git/javadoc/com/google/common/collect/BiMap.html).
* The subdirectory _jung/_ contains the collection of JARs needed to use the [JUNG library](http://jung.sourceforge.net/), which provide the graph data structure we will use to store our TSP instances and algorithms for solving many classical combinatorial problems on graphs including max flow min cut, minimum spanning tree, and connected components.
* The subdirectory _jgrapht/_ contains the collection of JARs needed to use the [JGraphT library](http://jgrapht.org/), another graphical library similar to JUNG. It is no longer under active development (so generally it is better to use JUNG), but it contains a few tools not included in JUNG that we will need for extracting [Eulerian tours](http://en.wikipedia.org/wiki/Eulerian_path).


# Algorithms and Data Structures

In algorithms and data structures implemented for you are located in the directories _tspSolver/src/instances/_ and _tspSolver/src/solver/_. Most importantly, the code here includes

* _src/instances/TspInstance.java_ provides the data structures used the store the graph and edge weights that are the inputs for solving a TSP problem. Some utility methods for computing properties of cuts and tours are also provided.
* _solver/Util.java_ provides a few methods for making the CPLEX API more Java friendly and integrating the various libraries we are using.
* _src/solver/MinCutSolver.java_ provides a light weight wrapper around the [JUNG implementation of the Edmonds Karp algorithm](http://jung.sourceforge.net/doc/api/edu/uci/ics/jung/algorithms/flows/EdmondsKarpMaxFlow.html) for solving max-flow min-cut.
* _src/solver/ChristofidesSolver.java_ provides an implementation of Christofides algorithm that can additionally take a set of suggested edges as a _hint_.
* _src/solver/TwoOpt.java_ provides a naïve implementation of the [two-opt](http://www.csc.kth.se/utbildning/kth/kurser/DD2440/avalg14/TSP-JohMcg97.pdf) local search algorithm.

To complete the tutorial, all you need to do is to put everything together. The file

_tspSolver/src/solver/TspIpSolver.java_

is the only file you will need to modify. It begins almost blank, and we provide step by step instructions on how to complete it.

# Testing and Execution

You will test your code on small hand coded test instances using the [JUnit test framework](http://www.vogella.com/tutorials/JUnit/article.html), located in _tspSolver/test/solver/_, and on larger instances in from the (famous) [TSPLIB](http://comopt.ifi.uni-heidelberg.de/software/TSPLIB95/) test set. The project is distributed with the TSPLIB data in the directory _tspSolver/sampleData/TSPLIB_. The code to create the `TspInstance` objects by parsing the data files is in _tspSolver/src/tspLib/TspLibParser.java_, but has already been called for you from _tspSolver/src/main/Main.java_, the entry point for the program.