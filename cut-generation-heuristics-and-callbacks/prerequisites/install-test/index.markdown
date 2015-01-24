---
layout: page
title:  "Test Your Installation"
author: Ross Anderson
---

# Test Eclipse

Open eclipse and select your workspace. If you start out with a blue screen with some circular buttons saying _Welcome_, _Tutorial_, etc., click Go to _Workbench_. Otherwise, you are already at the workbench. Go to _File_ → _New_ → _Java Project_. Name your project sandbox, then click the button Finish. You now have a new project!

In the Package Explorer (left side) right click the arrow next to sandbox to expand the project. Right click on the `src` folder and then go to _new_ → _class_. Name the class `TestClass`. Under "Which method stubs would you like to create?" check the box next to `public static void main(String[] args)`. Hit the _Finish_ button. Then replace

{% highlight java %}
// TODO Auto-generated method stub
{% endhighlight %}

by

{% highlight java %}
System.out.println("Hello World!");
{% endhighlight %}

Save the file `TestClass.java` by hitting _Ctrl+s_ or the save button (top left on the menu bar). To run your code, go to the top center of the menu bar and hit the run icon (its a green circle with a white arrow inside), or go to the menu bar and hit _Run_ → _Run_. The Console should pop up on the bottom with the output of your program.

# Test CPLEX from Eclipse

Open Eclipse and expand the sandbox project we created previously. Create a new class called `KnapsackSolver`. Next we are going to connect to CPLEX. Right click on sandbox in the package explorer and select _new_ → _folder_. Give it the name `lib` and click _Finish_. Find the file `cplex.jar` in your CPLEX installation.  Depending on your operating system, it should located at

* Windows: `C:\Program Files\IBM\ILOG\CPLEX_Studio125\cplex\lib\cplex.jar`
* Linux: `/opt/IBM/ILOG/CPLEX_Studio125/cplex/lib/cplex.jar`
* Mac: `/Users/<yourusername>/Applications/IBM/ILOG/CPLEX_Studio125/cplex/lib/cplex.jar`

Copy the file `cplex.jar` and paste it in the newly created `lib` folder in our project. Right click on sandbox in the package explorer, then select _Build Path_ → _Configure Build Path..._ Select the _Libraries_ tab, then hit the _Add JARs_ button (see [here](http://stackoverflow.com/questions/8486403/eclipse-ide-add-jar-add-external-jar-add-library) for an explanation of what all these buttons do). Select the file `sandbox/lib/cplex.jar`, then hit _OK_ until we have exited all the menus.

Open up `KnapsackSolver` and replace the text of the file with the following:

{% highlight java %}

import java.util.Arrays;
import ilog.concert.IloException;
import ilog.concert.IloIntVar;
import ilog.cplex.IloCplex;
 
public class KnapsackSolver {

  public static double[] solveKnapsack(double[] values, double[] weights,
                                       double capacity){
    if(weights.length != values.length){
      throw new RuntimeException("values.length was " + values.length
          + " and weights.length was" +  weights.length
          + " but they must be the same");
    }
    try {
      IloCplex cplex = new IloCplex();
      IloIntVar[] x = cplex.boolVarArray(values.length);
      cplex.addLe(cplex.scalProd(x, weights), capacity);
      cplex.addMaximize(cplex.scalProd(x, values));
      cplex.solve();
      return cplex.getValues(x);
    } catch (IloException e) {
      throw new RuntimeException();
    }
  }
 
  public static void main(String[] args){
    double[] values = new double[]{2,4,5,6};
    double[] weights= new double[]{2,3,4,7};
    double capacity = 8;
    double[] solution = solveKnapsack(values,weights,capacity);
    System.out.println(Arrays.toString(solution));
  }
}
{% endhighlight %}

Run the code. The final line of output should of course be `[0.0, 1.0, 1.0, 0.0]`, up to some precision. Upload the output of the program as your assignment to Stellar. If you get the error

`Exception in thread "main" java.lang.UnsatisfiedLinkError: ilog.cplex.Cplex.CPXopenCPLEX([I)J`

then your `PATH` was not set properly. The easiest way to deal with this is to add a virtual machine argument to the Run Configuration. Starting from the menu bar at the top, select _Run_ → _Run Configurations..._ and then on the left, make sure _Java Application_ → _KnapsackSolver_ is selected. In the center panel, pick the _( x )=Arguments_ tab. In the second box, titled _VM Arguments_, add the line:

* Linux: `-Djava.library.path=/opt/ibm/ILOG/CPLEX_Studio125/cplex/bin/x86-64_sles10_4.1`
* Windows: `-Djava.library.path="C:\Program Files\IBM\ILOG\CPLEX_Studio125\cplex\bin\x64_win64"`
* Mac: `-Djava.library.path=/Users/<yourusername>/Applications/IBM/ILOG/CPLEX_Studio125/cplex/bin/x86-64_darwin`

As you can see, the VM argument is telling java the directory that the c code of CPLEX is located in.

**Warning:** If your VM argument would have a space, you need to put quotes around it, as in the Windows example above.

Finally, hit _Run_ on the bottom.