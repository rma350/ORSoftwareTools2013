---
layout: page
title:  "Do you know Java?"
author: Ross Anderson
---

Below is a short tutorial designed for someone who is familiar with programming (potentially only at the level of MATLAB), and has never learned or long forgotten Java. After explaining the very basics of classes and objects, we skip all of the syntax and control flow and go straight to what you will need to complete the workshop. If your Java is not strong, please take the time to understand this tutorial, otherwise you will most likely be lost during the workshop. It should take about 30 minutes.

# Java Basics: classes, Objects, Fields, Methods, and the Keyword Static

In Java, all code is associated with a class. Classes have _fields_, where data is stored, and _methods_, that perform some computation based on input _arguments_ as well as the fields. _Instances_ of classes are created with _Constructors_ and called _Objects_. Once an Object has been constructed, its methods can be called and its fields can be accessed. Here is a concrete example with two simple classes:

---------------------

`Cat.java`

---------------------

{% highlight java %}
public class Cat {
  private int age;
  private String name;
  private Cat friend;
 
  public Cat(String name, int age) {
    this.name = name;
    this.age = age;
    friend = null;
  }
 
  public String getName() {
    return name;
  }
 
  public Cat getFriend() {
    return friend;
  }
 
  public void setFriend(Cat friend) {
    this.friend = friend;
  }
 
  public String sayHello(String aboutMe) {
    return "Hello World!  My name is " + 
      name + " and I am " + age + 
      " years old." + " I am " + aboutMe + ".";
  }
}
{% endhighlight %}

---------------------


---------------------

`CatsTheMusical.java`

---------------------

{% highlight java %}

public class CatsTheMusical {
 
  public static void main(String[] args) {
    Cat mist = new Cat("Mr. Mistoffelees", 8);
    System.out.println(mist.sayHello("magical"));
    Cat garf = new Cat("Garfield", 12);
    System.out.println(garf.sayHello("tired/hungry"));
    mist.setFriend(garf);
    System.out.println(mist.getName() + " and " + garf.getName()
        + " are friends? " + CatsTheMusical.areFriends(mist, garf));
    garf.setFriend(mist);
    System.out.println(mist.getName() + " and " + garf.getName()
        + " are friends? " + CatsTheMusical.areFriends(mist, garf));
  }
 
  public static boolean areFriends(Cat cat1, Cat cat2) {
    if (cat1.getFriend() != null && cat2.getFriend() != null) {
      return cat1.getFriend() == cat2 && cat2.getFriend() == cat1;
    }
    return false;
  }
}
{% endhighlight %}

---------------------

If you understand this code, particularly how the keywords this and static work, and you can predict the output, then you can skip ahead to the next section.  [View output here](cat-output/index.html).  Otherwise, read on.

First, lets talk about the contents of the files `Cat.java`.

* _Fields_: The class `Cat` has three fields, `name`, `age` and `friend`. Each field has a _type_, which ensures that we can only store a `String` in `name`, an `int` in `age` and another `Cat` in `friend`. The word `private` before each field indicates that the field can only be accessed from within the class `Cat`.
* _Constructors_: The class `Cat` has one constructor `public Cat(String name, int age){...}`. In the constructor, the fields `name` and `age` are initialized with the values given in the arguments, while `friend` is simply set to `null`. Because the constructor is `public`, it can be invoked from other classes. An example of the invoking the constructor can be seen in `CatsTheMusical.java`.
* _Methods:_ The class Cat has four methods, `getName(){...}`, `getFriend(){...}`, `setFriend(Cat friend){...}` and `sayHello(String aboutMe){...}`. The keyword `public` indicates that the methods can be called outside of the class `Cat`. The second word in each method is the _return type_, or the type of the result of the method. Notice that `setFriend(...)` has a return type of void, indicating that nothing is returned. The first two methods are examples of _getter methods_, as they simply retrieve the value of a private field. The third method is a _setter method_, which allows the field `friend` to be changed from outside the class `Cat`.

Now lets talk about the other file CatsTheMusical.java.

* _Static Methods_: Methods that are `static`, while associated with the class they are written in, are not associated with any particular instance. They can be invoked by using `[class-name].[method]`. For example, see the invocation of `areFriends(...)` within `main(...)`.
* `main(...)`: All Java program must start with the code `public static void main(String[] args){...}`. From here, we will follow the methods and constructors into other class files. The arguments, `String[]` args can be accessed by calling `args[0]` etc. and can be set by going to the top menu bar and then selecting _Run_ → _Run Configurations..._, then selecting the _Arguments_ tab in the center and filling out the _Program Arguments_ box.

Now that we have the basic terminology down, lets examine some of the finer points which may be confusing to non-Java programmers:

* Given an instance of a class (e.g. for `Cat c = new Cat(...)`, `c` is an instance), non-static methods from the class can be called by `[instance name].[method name]` (e.g. `c.getName()`). Similarly, non-static fields can be accessed by calling `[instance name].[field name]`, (e.g. `c.name`). However, methods or fields labeled private can only be called/accessed from code written within their own class file.
* Within a constructor, we can directly access the fields of a class by name without reference to any particular instance, as it is implied that the instance is the one that is being built. For example, in the `Cat` constructor, we make the assignment `friend = null`. However, when there are argument variables with the same name as the field variables, they take precedence and _shadow_ the fields variables. To reference the fields once they have been shadowed, you need to use the keyword `this`. In fact, you can use the keyword `this` whenever you want to refer to a field or method of the instance of the class being built by the constructor, regardless of whether there is shadowing. The same rules when you are inside a non-static method instead of inside a constructor.
* In static methods, we cannot refer to any non-static fields or non static methods of the class without also providing an instance, as static methods are not associated with any instance. Similarly, the keyword `this` cannot be applied.

Now you are ready for some more advanced Java!

# Recommended Reading

Types, Primitives, and Generics:

* Try [here](http://docs.oracle.com/javase/tutorial/java/generics/) for generics, up to Generics, Inheritance, and Subtypes.
* [Autoboxing](http://docs.oracle.com/javase/tutorial/java/data/autoboxing.html)

Inheritance, Abstract Classes, Interfaces, and Inner Classes:

* [Java tutorails](http://docs.oracle.com/javase/tutorial/java/IandI/index.html)

Data Structures:

* [The Java Collections documentation](http://docs.oracle.com/javase/tutorial/collections/index.html)
  * Interfaces: Sets, Lists, and Maps
  * Implementations: HashSet, ArrayList, HashMap

* [Guava](https://code.google.com/p/guava-libraries/wiki/GuavaExplained?tm=6)
  * [BiMap](https://code.google.com/p/guava-libraries/wiki/NewCollectionTypesExplained#BiMap)
  * [Immutable Collections](https://code.google.com/p/guava-libraries/wiki/ImmutableCollectionsExplained)
