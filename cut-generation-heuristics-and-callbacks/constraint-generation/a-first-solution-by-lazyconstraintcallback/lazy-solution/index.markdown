---
layout: page
title:  "LazyConstraintCallback Solution"
author: Ross Anderson
---


{% highlight java %}
@Override
protected void main() throws IloException {
  //fill this in
  Set<E> edgesUsed = edgesUsed(this.getValues(edgeVariablesAsArray));
  Set<Set<V>> connectedComponents =
    tspInstance.getConnectedComponents(edgesUsed);
  if(connectedComponents.size() > 1){
    for(Set<V> connectedComponent: connectedComponents){
      this.add(cplex.ge(Util.integerSum(cplex, edgeVariables,
        tspInstance.cutEdgonnectedComponent)),2));
    }      
  }
}
{% endhighlight %}

This solution is a little sloppy in that if there are only two connected components, the set of edges for their cut will be the same, but we will add the constraint twice.  To get around this, you could cache the sets of edges in a new HashSet, to automatically filter out duplicates, then add all the constraints once all the edge sets have been determined.  To think about: when there \\(n>2\\) connected components, are all of the constraints needed, or do any \\(n-1\\) of the constraints imply the final constraint?