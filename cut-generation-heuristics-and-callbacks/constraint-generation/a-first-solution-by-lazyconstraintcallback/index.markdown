---
layout: page
title:  "A First Solution by LazyConstraintCallback"
author: Ross Anderson
---

# Default CPLEX

Callbacks allow user written code to be run at select points to monitor or influence the behavior of the CPLEX solver.  Before modifying the behavior of the solver, lets understand exactly how the solver works.  Below is a somewhat simplified picture of the algorithm CPLEX is using to solve an integer program.

![Basic CPLEX control flow](../../../assets/cut-generation-heuristics-and-callbacks/constraint-generation/lazy-constraint-callback/no-callbacks.png)

The algorithm terminates when the node stack is empty, and the best incumbent found is the new solution. The first node pushed on, the LP relaxation of the original problem is often called the _root node_.

# Lazy Constraints in CPLEX

Lazy constraints, at a high level, are constraints that are only checked when a candidate for an integer solution has been identified.  They can be added to the model at the start though the `IloCplex` method `addLazyConstraint(IloRange range)`, or they can be added on the fly with a `LazyConstraintCallback` (documentation [here](http://pic.dhe.ibm.com/infocenter/cosinfoc/v12r5/index.jsp?topic=%2Filog.odms.cplex.help%2Frefjavacplex%2Fhtml%2Filog%2Fcplex%2FIloCplex.LazyConstraintCallback.html)).  The `LazyConstraintCallback` provides a user implemented routine to take a new potential incumbent solution, determine any of the lazy constraints were violated, and if so, to add at least one of them to the model.  Typically, you would want to use lazy constraints when you have a large number of constraints and you expect that few will be violated, as they allow you to have smaller LPs to solve when processing each node.  In the case of the `LazyConstraintCallback`, you gain an additional advantage in that you do not need to explicitly store all of the lazy constraints in memory, which is critical for problems like TSP that have an exponential number of constraints.  The downside is that you can waste a lot of time in branch and bound trying to generate integer solutions, only to find that they are actually infeasible.  Regardless of whether `addLazyConstraint(IloRange range))` or a `LazyConstraintCallback` was used, the model becomes

![Lazy constraint callback control flow](../../../assets/cut-generation-heuristics-and-callbacks/constraint-generation/lazy-constraint-callback/lazy-constraint-callback.png)



# Using LazyConstraintCallback

Qualitatively, the idea of the class `LazyConstraintCallback` is that you pass CPLEX the function meeting the following specification:

* **Input:** an integral solution to your IP that is guaranteed to satisfy all constraints except the Lazy Constraints not yet added
* **Output:** a (potentially empty) list of violated constraints that is guaranteed to be nonempty if at least one constraint was violated

However, in Java, we do not pass functions (methods), we pass objects satisfying some interface specifying methods.  This means we must create an object of type `LazyConstraintCallback`.  The class is `abstract`, (see [Java introduction](../../prerequisites/java/index.html)), so we must extend the class and fill in the abstract methods, where we will specify how to check for violated constraints.  Since `LazyConstraintCallback` is an abstract class, it comes with some methods already implemented:

|Method Name|Return Type|Arguments|Description|
|:----------|:----------|:--------|:----------|
|`getValue`|`double`|`IloNumVar var`|Returns the solution value of `var` at the current node.|
|`getValues`|`double[]`|`IloNumVar[] vars`|Returns the solution values for `vars` at the current node.|
|`add`|`IloRange`|`IloRange cut`|Adds the constraint `cut` to the model.  Assumes `cut` is linear.|

To generate the constraints we will add, we use the same `IloCplex` instance that is solving the IP.

**Warning:** Notice that `IloCplex` has methods **by the same name** for getting variable values and adding constraints to the model.  However, using these methods in the middle of a callback will cause an error.


**Info:** To make a \\(\ge\\) constraint in the middle of callback, be sure to call `cplex.ge()`, not `cplex.addGe()`, as discussed [here](../../getting-started/java-and-cplex#the-java-interface-to-cplex).

**Warning:** The method `getValue(IloNumVar v)` is significantly slower than `getValues(IloNumVar[] vars)`, as [previously discussed for IloCplex](../../getting-started/java-and-cplex#performance-issues-reading-variable-values-from-cplex).

It is convenient to make the new class an [inner class](http://docs.oracle.com/javase/tutorial/java/javaOO/nested.html) of the class where you are formulating your optimization problem, as then all of the fields which hold your problem data will be accessible.  In particular, you will need access to the `IloCplex` instance as well as whatever data structure you have storing your `IloIntVar` instances.

# TSP Integer "Solutions" Satisfying the Degree Constraints

To accomplish our task of identifying violated cutset constraints for integer solutions that are ensured to satisfy all of the degree constraints, we need to understand the combinatorial structure of these solutions to avoid checking all \\(2^\{\|V\|\}\\) constraints.  However, it is easy to see that in any (finite) graph where every node has degree two, the graph must be a collection of node disjoint cycles.  When the graph is a single cycle, none of the cutset constraints will be violated, and when it contains multiple cycles, we will have a violated constraint for each cycle, as there will be no edges across the cut separating the nodes in the cycle from the rest of the graph.  Thus given the \\(\|V\|\\) edges in a solution, we can identify all such cuts in \\(O(\|V\|)\\) by partitioning the graph into connected components.  Note however that each of the cuts constraints will have potentially \\(O(\|E\|)\\) nonzero coefficients.


# Implementing LazyConstraintCallback for the Cutset Constraints

First, we will extend `LazyConstraintCallback` with an innner class inside `TspIpSolver`.  At the bottom of `TspIpSolver.java` (but before the final '\}' ending the class), add the following code to extend `LazyConstraintCallback`:

{% highlight java %}
private class IntegerCutCallback extends LazyConstraintCallback{
  public IntegerCutCallback(){}
  @Override
  protected void main() throws IloException {
    //fill this in
  }      
}
{% endhighlight %}

Next, we need to tell our solver to use the IntegerCutCallback, but only the option `Option.lazy` is selected, otherwise we would break our unit test from the previous section!  At the end of the constructor for `TspIpSolver`, add

{% highlight java %}
if(options.contains(Option.lazy)){
  cplex.use(new IntegerCutCallback());
}
{% endhighlight %}

Now you need to fill in the method `main()` from the callback, which should check to see if the current integer solution violates any lazy constraints, and if so, add those constraints.  Here is an outline of one possible solution:

1. Get a `double[]` of all the edge variable values
2. Convert this into a `Set<E>` containing only those edges taking the value `1.0`
3. Form a the subgraph only containing the above edges
4. Run connected components on this graph to get the set of sets of connected components, a `Set<Set<V>>`
5. If there is only one connected component, then the solution is feasible, so return.
6. Otherwise, for each connected component, get all edges in the original graph with one endpoint on each side of the cut, and add a constraint saying that at least two of these edges must be used

Code has been provided to complete steps 3 & 4, and part of step 6 for you.  In the class TspInstance, we have the methods

|Method Name|Return Type|Arguments|Description|
|:----------|:----------|:--------|:----------|
|`getConnectedComponents`|`Set<Set<V>>`|`Set<E> includedEdges`|Using the undirected graph for this instance, forms a subgraph using all of the vertices but only `includedEdges`, then computes and returns the set of connected components of the subgraph|
|`cutEdges`|`Set<E>`|`Set<V> cutVertices`|Gets from the undirected graph from this instance all edges with exactly one endpoint in `cutVertices`.|

Here are a few more hints:

* We have already stored an array with all of the edge variables in `TspIpSolver`, `edgeVariablesAsArray`, which you can apply `getValues()` directly to.
* We already defined the method `edgesUsed(double[] edgeValues)` in the class `TspIpSolver` that takes in a double[] of the variable values of each edge, assuming they are in the same order as `edgeVariablesAsArray`, and returns edges where the variable value is (approximately) `1.0`.
* The method `integerSum` from class `Util` as introduced in [Java style for CPLEX](../../getting-started/java-style-for-cplex#good-style) should be useful.

[Solution is here](lazy-solution/index.html)


# Testing your solution

We will use the [same test instance](../../tsp-in-cplex/adding-variables-objectives-and-constraints/index.html#test-your-code) we used before we had implemented the cutset constraints.  Thus the first integer solution found should cause our model to add the cut below and ultimately reach a final solution with objective value 24.

![Lazy Cut](../../../assets/cut-generation-heuristics-and-callbacks/constraint-generation/lazy-constraint-callback/violated-cut.png)

The correct JUnit test has already been coded for you.  If you run the JUnit test `TspSolverTest`, now the second test, `testCutsetLazy()`, should pass in addition to the first test `testDegreeConstraints()`.

### Implementation Note

Ideally, it would have been better to make a function that takes in the set of edges used and returns the cuts as a set of set of edges.  Then we could test this function for a fixed input independently of CPLEX and solving the whole TSP.  If the code is designed as a bunch of modular, independent parts that are independently tested and simply connected together, it is much easier to determine where errors are coming from.  This idea is known as [Unit Testing](http://en.wikipedia.org/wiki/Unit_testing).

# Complete Source

If all else fails, _TspIpSolver.java_ should currently look something like [this](complete-source/index.html).