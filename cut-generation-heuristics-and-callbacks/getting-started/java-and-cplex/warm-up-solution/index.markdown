---
layout: page
title:  "Java and CPLEX Warm Up Solution"
author: Ross Anderson
---

{% highlight java %}
public class WarmUps {
 
  public static void main(String[] args) {    
    try {
      exerciseOne();
    } catch (IloException e) {
      throw new RuntimeException(e);
    }
  }
   
  public static void exerciseOne() throws IloException{
    IloCplex cplex = new IloCplex();
    IloIntVar x = cplex.boolVar();
    IloIntVar y = cplex.boolVar();
    IloIntVar z = cplex.boolVar();
    IloLinearIntExpr constraintSum = cplex.linearIntExpr();
    constraintSum.addTerm(x,1);
    constraintSum.addTerm(y,1);
    constraintSum.addTerm(z,1);
    cplex.addGe(constraintSum,2);
    IloLinearIntExpr objectiveSum = cplex.linearIntExpr();
    objectiveSum.addTerm(x,1);
    objectiveSum.addTerm(y,2);
    objectiveSum.addTerm(z,3);
    cplex.addMinimize(objectiveSum);
    cplex.solve();
    System.out.println("x: " + cplex.getValue(x));
    System.out.println("y: " + cplex.getValue(y));
    System.out.println("z: " + cplex.getValue(z));
    System.out.println("obj: " + cplex.getObjValue());
  }
}
{% endhighlight %}