---
layout: page
title:  "Java Style for CPLEX"
author: Ross Anderson
---

# A Road to Trouble

When programming in Java, you generally have [collections](http://docs.oracle.com/javase/tutorial/collections/) of objects, as [previously discussed](../../prerequisites/java/index.html). Often, you want to create a variable (or constraint) for element of a collection. A **bad** approach is illustrated below:

{% highlight java %}
//assume "makeSomeStrings()" is defined elsewhere
Set<String> stringCollection = makeSomeStrings();
IloCplex cplex = new IloCplex();
String[] stringToIndex = new String[stringCollection.size()];
IloIntVar[] variablesToIndex = cplex.boolVarArray(stringCollection.size());
int i = 0;
for(String s: stringCollection){
  stringToIndex[i++] = s;
}
{% endhighlight %}

Then given a `String` from the collection, we could access the corresponding `IloIntVar`, and with an `IloIntVar`, we would access the corresponding `String`, with the following snippets

{% highlight java %}
public IloIntVar getVariableForString(String s, String[] stringToIndex,
                                      IloIntVar[] variablesToIndex){
  for(int i = 0; i < stringToIndex.length; i++){
    if(stringToIndex[i].equals(s)){
      return variablesToIndex[i];
    }
  }
  return null;
}

public String getStringForVariable(IloIntVar v, String[] stringToIndex,
                                   IloIntVar[] variablesToIndex){
  for(int i = 0; i < variablesToIndex.length; i++){
    if(variablesToIndex[i] == v){
      return stringToIndex[i];
    }
  }
  return null;
}
{% endhighlight %}

There are several reasons to be concerned with this.

* For \\(n\\) variables, access time takes \\(O(n)\)).
* We are maintaining two data structures and an index by hand, which leave a lot of room for programmer error
*We cannot add new variables at a later date

We can eliminate any chance of an indexing error and improve our access time to \\(O(1)\\) by replacing our two arrays by two [HashMaps](http://docs.oracle.com/javase/6/docs/api/java/util/HashMap.html), e.g.

{% highlight java %}
//assume "makeSomeStrings()" is defined elsewhere
Set<String> stringCollection = makeSomeStrings();
IloCplex cplex = new IloCplex();
Map<String,IloIntVar> stringToVariable = new HashMap<String,IloIntVar>();
Map<IloIntVar,String> variableToString = new HashMap<IloIntVar,String>();
for(String s: stringCollection){
  IloIntVar v = cplex.boolVar();
  stringToVariable.put(s,v);
  variableToString.put(v,s);
}
{% endhighlight %}

However, we are still maintaining two separate data structures which we need to keep synchronized, which is asking for trouble.

# Good Style

Instead, we use [Guava's](http://code.google.com/p/guava-libraries/wiki/GuavaExplained?tm=6) special data structure, the [ImmutableBiMap](http://docs.guava-libraries.googlecode.com/git/javadoc/com/google/common/collect/ImmutableBiMap.html), which will maintain two `HashMap`s for us (and as a bonus, prevent any accidental modifications once we _build_ the map).  The following methods can be found in `Util.java` in your project:

{% highlight java %}
public static <T> ImmutableBiMap<T,IloIntVar> makeBinaryVariables(
    IloCplex cplex, Iterable<T> set) throws IloException{
  Builder<T,IloIntVar> ans = ImmutableBiMap.builder();
  for(T t: set){
    ans.put(t, cplex.boolVar());
  }
  return ans.build();
}
{% endhighlight %}

Now the unfortunate code from before can be replaced by

{% highlight java %}
//assume "makeSomeStrings()" is defined elsewhere
Set<String> stringCollection = makeSomeStrings();
IloCplex cplex = new IloCplex();
ImmutableBiMap<String,IloIntVar> stringVarBiMap =
  Util.makeBinaryVariables(cplex,stringCollection);
for(String s: stringCollection){
  stringVarMap.put(s,cplex.boolVar());
}
 
public IloIntVar getVariableForString(
    String s, ImmutableBiMap<String,IloIntVar> stringVarBiMap){
  return stringVarBiMap.get(s);
}
public String getStringForVariable(
    IloIntVar v, ImmutableBiMap<String,IloIntVar> stringVarBiMap){
  return stringVarBiMap.inverse().get(v);
}
{% endhighlight %}

There are a few additional methods in the class `Util` for creating `IloLinearIntExpr` objects and `IloLinearNumExpr` objects designed to keep your code organized and error free. They use another Guava class, [Function](https://code.google.com/p/guava-libraries/wiki/FunctionalExplained), (see [here](http://docs.guava-libraries.googlecode.com/git/javadoc/com/google/common/base/Function.html) for Javadoc). The idea of a `Function<F,T>` is simple, they take in any object of type `F` and produce some object of type `T`. Functions are a little clunky to make (a weakness of Java), but fortunately you won't have to make many. The following static functions are also found in `Util`:

|Method Name|Return Type|Arguments|Description|
|:----------|:----------|:--------|:----------|
|`integerSum`|`IloLinearIntExpr`|`IloCplex cplex, BiMap<T,IloIntVar> variables, Iterable<T> set`|For each \\(e \in \\) `set` finds the variable \\(x_e\\) in `variables` and returns \\(\sum_{e \in \text{set}} x_e\\)|
|`integerSum`|`IloLinearIntExpr`|`IloCplex cplex, BiMap<T,IloIntVar> variables, Iterable<T> set,Function<? super T,Integer> coefficients`|For each \\(e \in \\) `set` finds the variable \\(x_e\\) in `variables` and \\(c_e\\) by applying `coefficients` to \\(e\\) and returns \\(\sum_{e \in \text{set}} c_e x_e\\)|
|`calcSum`|`double`|`Set<E> terms, Map<E,Double> coefficients`|If for every \\(e \in\\) `terms`, we let \\(c_e\\) be zero if coefficients does not contain the key \\(e\\) and the value of `coefficients[`\\(e\\)`]` otherwise, returns \\(\sum_{e \in \text{terms}} c_e\\).|

While the final function actually has nothing to do with CPLEX, it will often be useful when using CPLEX.

# Simple Example Revisited

Recall the IP we modeled with CPLEX in the previous section:

$$
\begin{align}
&\min & x + 2y + 3z\\
&\text{subject to}& x + y + z &\geq 2\\
&& x,y,z &\in\{0,1\}
\end{align}
$$

Lets design a more scalable implementation using our new methods. Finish the method `exercise2()` from `WarmUps.java`, which currently reads

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
}
{% endhighlight %}

The functionality should be the same as `exercise1()`. Modify the `main` method to test your code.

[Solution here](simple-example-revisited-solution/index.html).