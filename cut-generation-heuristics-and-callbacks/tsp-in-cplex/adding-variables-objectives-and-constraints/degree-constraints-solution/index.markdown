---
layout: page
title:  "Degree Constraints Solution"
author: Ross Anderson
---

{% highlight java %}
//the degree constraints
for(V vertex: graph.getVertices()){
    cplex.addEq(Util.integerSum(cplex, edgeVariables, 
            graph.getIncidentEdges(vertex)), 2);
}
{% endhighlight %}