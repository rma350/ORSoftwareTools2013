---
layout: page
title:  "Adding Variables, Objectives, and Constraints"
author: Ross Anderson
---

# Problem Formulation

First, we will add the following fields to the class so we can easily reference our problem data from any method. Add

{% highlight java %}
private EnumSet<Option> options;
private IloCplex cplex;
private TspInstance<V,E> tspInstance;
private ImmutableBiMap<E,IloIntVar> edgeVariables;
{% endhighlight %}

and initialize them, as below.

{% highlight java %}
public TspIpSolver(TspInstance<V,E> tspInstance, EnumSet<Option> options)
    throws IloException{
  this.options = options;
  this.tspInstance = tspInstance;
  this.cplex = new IloCplex();
  //for convenience, we will be using this a lot
  UndirectedGraph<V,E> graph = tspInstance.getGraph(); 
  this.edgeVariables = Util.makeBinaryVariables(cplex, graph.getEdges());
  //the degree constraints
  //the objective     
}
{% endhighlight %}

Next, we need to add the objective and the degree constraints to the `IloCplex` instance. Try adding them yourself! The following methods (as defined in [Solver Specification](../solver-specification/index.html) and [Java Style for CPLEX](../../getting-started/java-style-for-cplex/index.html#good-style) should be useful for making the degree constraints:

* From `Util`, the `static` method `integerSum(IloCplex cplex, BiMap<T,IloIntVar> variables, Iterable<T> set)`
* From `IloCplex`, the method `addEq(IloNumExpr e, double v)`
* From `UndirectedGraph<V,E>`, the method `getIncidentEdges(V vertex)`

[View solution here](degree-constraints-solution/index.html).

For the objective, we need the functions:

* From `Util`, the `static` method `sum(IloCplex cplex, BiMap<T,IloIntVar> variables, Iterable<T> set, Function<? super T,? extends Number> coefficients)`
* From `UndirectedGraph<V,E>`, the method `getEdges()`
* From `TspInstance`, the method `getEdgeWeights()`

[View solution here](objective-solution/index.html).

# Solving and Extracting the Solution

Here we fill in the blank methods `solve()`, `getEdgesInOpt()`, and `getOptVal()`. For performance reasons, it is a good idea to cache the solution as a field of the `TspIpSolver` and "shut down" CPLEX. Recall [the warning](../../getting-started/java-and-cplex#performance-issues-reading-variable-values-from-cplex) that calling `getValue(IloIntVar var)` from `IloCplex` repeatedly instead of a single `getValues(IloIntVar[] vars)` causes performance issues. Although it isn't really good "Java style," as discussed in [Java Style for CPLEX](../../getting-started/java-style-for-cplex), we will keep a copy of the edge variables stored in an array as a field of the class to improve performance.

To accomplish this, first add the fields

{% highlight java %}
private ImmutableSet<E> edgesInOpt;
private double optVal;
private IloIntVar[] edgeVariablesAsArray;
{% endhighlight %}

Next, we will set the field `edgeVariablesAsArray` by adding the one-liner to the constructor:

{% highlight java %}
...
this.edgeVariables = Util.makeBinaryVariables(cplex, graph.getEdges());
//insert this line
edgeVariablesAsArray = edgeVariables.inverse().keySet()
                                    .toArray(new IloIntVar[]{});
//the degree constraints
...
{% endhighlight %}

We will fill in the remaining fields during `solve()`. But first, we add an auxiliary method to encapsulate access to `edgeVariablesAsArray`, reducing the chance of error down the road. In designing this method, we must remember our previous discussion of [numerical tolerances](../../getting-started/java-and-cplex#reading-integer-variable-values-from-cplex).

{% highlight java %}
/**
 * assumes  edgeVarVals[i] in {0,1}, up to some tolerance.  
 * @param edgeVarVals
 * @return the edges where the variable takes the value one.
 */
private Set<E> edgesUsed(double[] edgeVarVals){
  Set<E> ans = new HashSet<E>();
  for(int e = 0; e < edgeVarVals.length; e++){
    if(Util.doubleToBoolean(edgeVarVals[e])){
      ans.add(edgeVariables.inverse().get(edgeVariablesAsArray[e]));
    }
  }
  return ans;
}
 
public void solve() throws IloException{
  if(!cplex.solve()){
    throw new RuntimeException();
  }
  optVal = cplex.getObjValue();
  edgesInOpt = ImmutableSet.copyOf(edgesUsed(cplex.getValues(
                                               edgeVariablesAsArray)));
  cplex.end();
}
 
public ImmutableSet<E> getEdgesInOpt(){
  return edgesInOpt;
}
 
public double getOptVal(){
  return optVal;
}
{% endhighlight %}

The method `edgesUsed()` make look a little odd right now, but its value will become apparent in the next section.

# Test Your Code

Now is a good time to test the code you have written thus far. Keep in mind that we haven't implemented the _cutset_ constraints yet. A unit test `testDegreeConstraints()` was made exactly for this purpose, in _tspSolver/test/solver/TspSolverTest.java_. Run the class as a JUnit test (right click on the class in the _Project Explorer_ and select _Run As_ → _JUnit Test_. If you are not on Windows, again you likely will need to set a VM argument as [previously discussed](../../prerequisites/install-test/index.html). The test `testDegreeConstraints()` should now pass.

If you need to debug, the unit test is on the following graph:

![Unit Test Instance with Solution](../../../assets/cut-generation-heuristics-and-callbacks/tsp-in-cplex/variables-objectives-and-constraints/solution.png)