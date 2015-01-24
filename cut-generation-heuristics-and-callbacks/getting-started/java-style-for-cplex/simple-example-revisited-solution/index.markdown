---
layout: page
title:  "Simple Example Revisited Solution"
author: Ross Anderson
---

{% highlight java %}

public static void exerciseTwo() throws IloException{
  List<String> varNames = Arrays.asList("x","y","z");
  Map<String,Integer> weightsMap = new HashMap<String,Integer>();
  weightsMap.put("x",1);
  weightsMap.put("y",2);
  weightsMap.put("z",3);
  Function<String,Integer> weights = Functions.forMap(weightsMap);
  IloCplex cplex = new IloCplex();
  //write code here!
  ImmutableBiMap<String,IloIntVar> varMap =
    Util.makeBinaryVariables(cplex,varNames);
  cplex.addGe(Util.integerSum(cplex,varMap,varNames),2);
  cplex.addMinimize(Util.integerSum(cplex,varMap,varNames,weights));
  cplex.solve();
  for(String varName: varNames){
    System.out.println(varName +": " + cplex.getValue(varMap.get(varName)));
  }
  System.out.println("obj: " + cplex.getObjValue());
}
{% endhighlight %}