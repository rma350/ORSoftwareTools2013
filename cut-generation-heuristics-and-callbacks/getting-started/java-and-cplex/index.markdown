---
layout: page
title:  "Java and CPLEX"
author: Ross Anderson
---

# The Java Interface to CPLEX

The use of CPLEX in Java is based around the class `IloCplex` ([documented here](http://www-01.ibm.com/support/knowledgecenter/SSSA5P_12.5.1/ilog.odms.cplex.help/refjavacplex/html/ilog/cplex/IloCplex.html)). The basic idea is that you create an `IloCplex` object for your optimization problem, then add variables, the objective, and constraints using methods in the class `IloCplex`. The `IloCplex` object can produce [IloNumVar](http://www-01.ibm.com/support/knowledgecenter/SSSA5P_12.5.1/ilog.odms.cplex.help/refjavacplex/html/ilog/concert/IloNumVar.html) objects and their subclass [IloIntVar](http://www-01.ibm.com/support/knowledgecenter/SSSA5P_12.5.1/ilog.odms.cplex.help/refjavacplex/html/ilog/concert/IloIntVar.html) objects, when are then used as arguments to further methods from `IloCplex` to make the objective and constraints. The `IloCplex` interface is pretty confusing. It is very large, has lots of redundant methods, and has lots of methods that appear to be the same but produce very different results. We now summarize the methods of `IloCplex` which will be of use to us:

|Name|Return Type|Arguments|Description|
|:-------|:--------------|:------------|:--------------|
|`boolVar`|`IloIntVar`| |Creates and returns a new Boolean variable (domain 0,1).|
|`boolVarArray`|`IloIntVar[]`|`int n`|Creates and returns an array of n new Boolean variables (domain 0,1)|
|`linearIntExpr`|`IloLinearIntExpr`| |Creates and returns an integer linear expression initialized as 0 (zero).|
|`addGe`|`IloRange`|`IloNumExpr e, double v`|Creates and returns a range representing the constraint \\(e \geq v\\), and adds constraint to model.|
|`addEq`|`IloRange`|`IloNumExpr e, double v`|Creates and returns a range representing the constraint \\(e = v\\), and adds constraint to model.|
|`ge`|`IloRange`|`IloNumExpr e, double v`|Creates and returns a range representing the constraint \\(e \geq v\\) **without** adding constraint to model.|
|`addMinimize`|`IloObjective`|`IloNumExpr e`|Creates and returns an objective to minimize the expression and adds it to the invoking model.|
|`sovle`|`boolean`| |Solves the active model.  Returns true if a feasible solution was found.|
|`getValue`|`double`|`IloNumVar var`|Returns the solution value for var.|
|`getValues`|`double`|`IloNumVar[] vars`|Returns the solution values for each of vars.|
|`getObjVal`|`double`| |Returns the objective value of the current solution.|


**Warning:** For an `IloCplex cplex`, an `IloNumExpr e` and a `double v`, calling `cplex.addGe(e,v)` and `cplex.addGe(v,e)` are both allowed but **do not produce the same result**! The first gives the constraint \\( e \geq v \\) while the second gives \\(v \geq e\\).

**Warning:** For an `IloCplex cplex`, an `IloNumExpr e` and a `double v`, calling `cplex.ge(e,v)` and `cplex.addGe(e,v)` are both allowed but do not produce the same result! While both return an object for the constraint \\(e \geq v\\), only the latter adds the constraint to the model. We will actually have use `cplex.ge(e,v)` later when we add constraints through callbacks instead of adding them directly to the model.

Notice that the various numeric expressions are arguments for these functions. The inheritance relationship between the different classes of numeric expressions is a little complicated, but well designed. They are summarized in the chart below, with an arrow from interface A to interface B if A implements B (is a subinterface, like a subclass).

![Inheritance hierarchy for CPLEX numeric expressions](../../../assets/cut-generation-heuristics-and-callbacks/getting-started/java-and-cplex/cplex-numeric-expression-inheritance.png)


We will use a few methods from IloLinearIntExpr to build up sums.

|Name|Return Type|Arguments|Description|
|:-------|:--------------|:------------|:--------------|
|`addTerm`|`void`|`IloIntVar v, int c`|Adds the new term \\(c\cdot v\\) to a scalar product. This method can create duplicate terms \\(\cdots + a_i \cdot x + \cdots + a_k \cdot x + \cdots\\) that could be joined to a single term \\(\cdots + (a_i + a_k) \cdot x + \cdots\\). Duplicates do not generate errors but require more memory and more running time.|

The interface for IloLinearNumExpr is similar.

# A Short Example

Suppose we want to solve the following IP in CPLEX:

$$
\begin{align}
&\min & x + 2y + 3z\\
&\text{subject to}& x + y + z &\geq 2\\
&& x,y,z &\in\{0,1\}
\end{align}
$$

In the file _src/main/WarmUps.java_, try to solve the above IP inside the method `exerciseOne()` and print out the values of the variables and the optimal solution.  Then run the code.  If you are on Linux or Mac, you will probably need to set a virtual machine argument as you did in [the installation assignment](../../prerequisites/install-test/index.html).  You can also look at the installation assignment if you need to see an example.

[Solution here](warm-up-solution/index.html).

# Reading Integer Variable Values from CPLEX

After solving an LP that is integral or an IP, you often want to query a variable and take some action if the variable has value 1.0, and another action if the variable has value 0.0. However, due to the [nature of floating point arithmetic](http://en.wikipedia.org/wiki/Floating_point#Accuracy_problems), there are often small rounding errors in the values produced by CPLEX.  As a result, to test if a variable is equal to zero, we should not write

{% highlight java %}
IloCplex cplex = new IloCplex();
IloIntVar var = cplex.boolVar();
//...
//solve some problem
//...
double val = cplex.getValue(var);
if(val == 0){
  //take some action
}
{% endhighlight %}

Instead, you need to allow for a small error, like so:

{% highlight java %}
IloCplex cplex = new IloCplex();
IloIntVar var = cplex.boolVar();
//...
//solve some problem
//...
double val = cplex.getValue(var);
if(Math.abs(val) < 0.00001){
  //take some action
}
{% endhighlight %}

The level of numerical tolerance and a variety of methods to extract integer values up to a tolerance are provided for you with the following static methods from _Util.java_

|Name|Return Type|Arguments|Description|
|:-------|:--------------|:------------|:--------------|
|`doubleToBoolean`|`boolean`|`double v`|Returns true if \\(\|v-1\| < \epsilon\\), false if \\(\|v\| < \epsilon\\), and throws an exception otherwise.  A static field in the class `Util` sets \\(\epsilon\\).|

# Performance Issues Reading Variable Values from CPLEX

Consider the following piece of code you can find in your _WarmUps.java_:

{% highlight java %}
public static void testSpeed() throws IloException{
  IloCplex cplex = new IloCplex();
  IloIntVar[] variables = cplex.boolVarArray(2000000);
  cplex.addMaximize(cplex.sum(variables));
  cplex.solve();
  {       
    System.err.println("testing group access");
    long startTime = System.currentTimeMillis();
    double sum = 0;
    double[] vals = cplex.getValues(variables);
    for(int i = 0; i < vals.length; i++){
      sum+= vals[i];
    }
    long endTime = System.currentTimeMillis();
    System.err.println("group: " + (endTime-startTime));
    System.err.println(sum);
  }
  {
    System.err.println("testing individual access");
    long startTime = System.currentTimeMillis();
    double sum = 0;
    for(int i = 0; i < variables.length; i++){
      double val = cplex.getValue(variables[i]);
      sum+=val;
    }
    long endTime = System.currentTimeMillis();
    System.err.println("individual: " + (endTime-startTime));
    System.err.println(sum);
  }   
}
{% endhighlight %}

It solves a very simple IP, then compares the amount of time to query the variables one at a time versus the amount of time needed to query all of the variables at once. Adjust the main method to run `testSpeed()` instead of `exercise1()`. The result should be surprising! On my machine, the group access took 23 milliseconds, while the individual access took 282 milliseconds, over ten times as long. If the variables were further wrapped in a `BiMap` (as suggested in the next section), the running time increases to over 500 milliseconds. If we are going to only read the values of the variables once, we wouldn't really care, as a typical IP with 2,000,000 variables takes much longer than half a second to solve. However, if instead we are querying the values of the variables at every node in the branch and bound tree to test for separation (as we will be soon), then these half seconds add up quickly. Know when to optimize!

**Warning:** The method `getValue(IloNumVar var)` from class `IloCplex` is much slower than `getValues(IloIntVar[] vars)`.