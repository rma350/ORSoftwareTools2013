---
layout: page
title:  "Complete Source after adding Lazy Constraints"
author: Ross Anderson
---

{% highlight java %}
public class TspIpSolver<V,E> {
  
  public static enum Option{
    lazy,userCut, randomizedUserCut, christofidesApprox,
        christofidesHeuristic,twoOpt,incumbent;
  }
  
  private EnumSet<Option> options;
  private IloCplex cplex;
  private TspInstance<V,E> tspInstance;
  private final ImmutableBiMap<E,IloIntVar> edgeVariables;
  private ImmutableSet<E> edgesInOpt;
  private double optVal;
  private IloIntVar[] edgeVariablesAsArray;
  
  
  public TspIpSolver(TspInstance<V,E> tspInstance) throws IloException{
    this(tspInstance,EnumSet.of(Option.lazy, Option.userCut,
                                Option.christofidesApprox,
                                Option.christofidesHeuristic));
  }
  
  public TspIpSolver(TspInstance<V,E> tspInstance, EnumSet<Option> options)
      throws IloException{
    this.options = options;
    this.tspInstance = tspInstance;
    this.cplex = new IloCplex();
    //for convenience, we will be using this a lot
    UndirectedGraph<V,E> graph = tspInstance.getGraph(); 
    this.edgeVariables = Util.makeBinaryVariables(cplex, graph.getEdges());
    edgeVariablesAsArray = edgeVariables.inverse().keySet()
                                        .toArray(new IloIntVar[]{});
    //the degree constraints
    for(V vertex: graph.getVertices()){
      cplex.addEq(Util.integerSum(cplex, edgeVariables, 
          graph.getIncidentEdges(vertex)), 2);
    }
    //the objective
    cplex.addMinimize(Util.integerSum(
        cplex, edgeVariables, graph.getEdges(),tspInstance.getEdgeWeights()));
    if(options.contains(Option.lazy)){
      cplex.use(new IntegerCutCallback());
    }
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
  
  /**
   * assumes  edgeVarVals[i] in {0,1}, up to some tolerance.  
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
  
  private class IntegerCutCallback extends LazyConstraintCallback{
    
    public IntegerCutCallback(){}

    @Override
    protected void main() throws IloException {
      Set<E> edgesUsed = edgesUsed(this.getValues(edgeVariablesAsArray));     
      Set<Set<V>> connectedComponents = tspInstance.getConnectedComponents(
                                                      edgesUsed);
      if(connectedComponents.size() > 1){
        for(Set<V> connectedComponent: connectedComponents){
          this.add(cplex.ge(Util.integerSum(cplex, edgeVariables,
                              tspInstance.cutEdges(connectedComponent)),2));
        }
      }
    }      
  }
}
{% endhighlight %}