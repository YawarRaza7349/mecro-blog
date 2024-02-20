- The Singleton pattern is *not* about how to ensure a class only has one object; "Singleton" merely describes the solution, not the problem. The actual problem the Singleton pattern solves is this: suppose you *already* have a static class with global state (the Singleton pattern doesn't *tell* you to use global state), but you want it to satisfy an interface (like if you had two static classes with the same methods and an algorithm that's identical for both of them). Static classes can't satisfy an interface, so the Singleton pattern reifies the static class into a first-class object which can. The Singleton pattern can also be used for lazy initialization, but I think the polymorphism thing is probably not as well known, plus I find polymorphism more interesting than laziness.

- Speaking of polymorphism, Bob "munificent" Nystrom wrote a blog post last year where he said he didn't want to use subtype polymorphism as his language's mechanism for heterogenous types because he didn't want the complexity of subtyping. Except, subtype polymorphism is not actually about subtyping! It's about dynamic dispatch and injecting different implementations into a shared type. This doesn't require subtyping at all, as those implementations don't each need their own subtype in addition to the shared type (just like how the cases of a sum type don't each have their own subtype). Here's an example:
```java
interface Shape {
  double area();
  double perimeter();
}

// invented keyword `impl`, implements exactly one interface
impl Circle implements Shape {
  double radius;
  Circle(double r) { radius = r; }
  public double area() { return Math.pi * radius * radius; }
  public double perimeter() { return 2 * Math.pi * radius; }
}

class Main {
  public static void main(String[] args) {
    // not allowed, Circle is not a type
    // Circle c = new Circle(48.0);
    
    // allowed, Circle constructor returns type Shape
    Shape c = new Circle(48.0);
    System.out.println(c.area() / c.perimeter());
  }
}
```

- Here's a way to think about the difference between Scala (2?)'s `trait A { (self: B) => /* ... */ }` and `trait A extends B { /* ... */ }`. In the former, a class that extends `A` must also extend `B`. In the latter, a class that extends `A` must implement all unimplemented methods of `B`, but does not explicitly extend `B` itself. Thus, the former imposes a *nominal* requirement on the class that extends it, while the latter imposes a *structural* requirement instead. So you can think of the latter as "exploding" the type `B` (which `A` provides its subclasses) into the methods of `B` (which `A` requires from its subclasses).
