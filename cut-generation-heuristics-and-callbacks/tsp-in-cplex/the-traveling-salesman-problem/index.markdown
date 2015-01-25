---
layout: page
title:  "The Traveling Salesman Problem"
author: Ross Anderson
---

The Traveling Salesmen Problem (TSP) is as follows: given a set of cities, or vertices \\(V\\) a set of direct routes between cities, or edges \\(E\\), and for each edge \\(e\\) a distance \\(d_e\\), what is the shortest tour through all the cities that visits each city exactly once? As this problem is quite difficult, we often consider the special case where \\(d_e\\) is assumed to be [metric](http://en.wikipedia.org/wiki/Metric_(mathematics)#Definition), the metric TSP.

One good integer programming formulation for TSP is the _cutset_ formulation. For any \\(S \subset V\\), let \\(\delta(S) =\{(u,v) \in E \mid u \in S, v \not \in S\} \\), i.e. those edges with exactly one endpoint in \\(S\\). With a slight abuse of notation, for \\(v \in V\\) we also let \\(\delta(v) = \delta(\{v\})\\), i.e. the edges incident to \\(v\\). The cutset formulation is:
    
$$
\begin{align} 
&\min & \sum_{e \in E} d_e x_e\\ 
&\text{subject to}& \sum_{e \in \delta(v)} x_e &= 2&v&\in V\\ 
&& \sum_{e \in \delta(S)} x_e &\geq 2& S &\subset V&S&\neq V,\, \emptyset\\ 
&& x_e &\in\{0,1\}&e&\in E 
\end{align} 
$$
 
The first family of constraints say that every node must have degree two, and will be referred to as the **degree constraints**. The second family of constraints say that for any cut separating the graph into two subsets, there must be at least two edges in use across the cut, and will be referred to as the **cutset constraints**. The solution to the linear programming relaxation of this formulation (when the integrality constraints are dropped) is known as the Held-Karp lower bound. Notice that there are exponentially many subsets of \\(V\\), (exactly \\(2^\{\|V\|\}\\)), and thus our integer programming formulation has exponentially many constraints.