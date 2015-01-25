---
layout: page
title:  "Objective Solution"
author: Ross Anderson
---

{% highlight java %}
//the objective
cplex.addMinimize(Util.integerSum(
        cplex, edgeVariables, graph.getEdges(),tspInstance.getEdgeWeights()));
}
{% endhighlight %}