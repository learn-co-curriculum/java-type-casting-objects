# Review Type Casting

## Learning Goals

- Demonstrate typecasting with inheritance hierarchies

## Introduction

We have already seen in previous lesson how type casting works with
primitive variables.  We also looked at autoboxing and unboxing between
a primitive type and their corresponding wrapper class. Now
let us focus on type casting between objects!

## Type Casting in Java

Consider the `Animal` hierarchy:

![Animal Hierarchy](https://curriculum-content.s3.amazonaws.com/6677/pillars/animal_hierarchy.png)

The `Animal` class is the superclass with
two subclasses extending it: the `Cat` class and the `Parrot` class.
We will use this hierarchy as an example to show how downcasting and upcasting
work with instances of this class hierarchy.

```java
public class Animal {
    private int age;
    public int getAge() {return age;}
    public void setAge(int age) {this.age = age;}
}
```

```java
public class Cat extends Animal {
    public void purr() {System.out.println("purr purr purr");}
}
```

```java
public class Parrot extends Animal {
    public void giveCracker() {System.out.println("yummy");}
}
```

### Upcasting or Widening

As we recall, widening or upcasting is like taking the contents from a smaller
box and placing them in a larger box - we know all the contents will definitely
fit because they originally fit in a smaller box.

When we declare a variable of type `Animal`:

```java
Animal myAnimal;
```

We can assign it an `Animal` instance:

```java
myAnimal = new Animal();
```

Or we can assign it a `Cat` or a `Parrot` instance because both cats
and parrots are considered animals. We can be confident that anything an `Animal` object
would need will be part of the definition of `Cat` or a `Parrot` object,
such as setting and getting the age.

```java
Animal myAnimal;
myAnimal = new Cat();
myAnimal.setAge(5);
System.out.println(myAnimal.getAge());
```

This is a prime example of upcasting and is a valid promotion.
We can assign a variable declared with a superclass type to an object
that is an instance of a subclass.  

```java
Superclass obj = new Subclass();   //ok to upcast
```

Java considers this type of casting a "safe operation", i.e. it can never fail,
because the subclass `Cat` inherits fields and methods from the superclass `Animal`.

But what if we want to call the `purr()` method on the `Cat` object?
`Animal` holds fewer fields and methods than the `Cat` class since it is the superclass.
So even though the `myAnimal` variable references a `Cat` object,
the `myAnimal` variable is still considered to
be of type `Animal`, so the fields and methods that are part of the `Cat`
type are not accessible using the `myAnimal` variable.

For example, this code won't compile since the declared type of `myAnimal`
is `Animal`, not `Cat`:

```java
public static void main(String[] args) {
    Animal myAnimal;
    myAnimal = new Cat();
    myAnimal.setAge(5);
    System.out.println(myAnimal.getAge());

    //compile-time error
    myAnimal.purr();
}
```


Why would we want to prevent this? Consider the following example.
The code randomly assigns an `Animal` or `Cat` to the
`myAnimal` variable.  While it is safe to call `setAge` and `getAge`
due to inheritance, it may not be safe to call `purr`.

```java
public static void main(String[] args) {
    Animal myAnimal;
    Random rand = new Random();
    if (rand.nextBoolean()) {
        myAnimal = new Cat();
    }
    else {
        myAnimal = new Animal();
    }
    myAnimal.setAge(5);
    System.out.println(myAnimal.getAge());

    //compile-time error
    myAnimal.purr();
}
```


### Downcasting or Narrowing

The reverse, or downcasting (also known as narrowing) is not always a "safe
operation." We can think about it like this: every cat is an animal, but not
every animal is a cat. The latter is what we are dealing with when we attempt to
downcast an object.

We have to make sure that the object we are downcasting is compatible with the
variable we are trying to assign it to. One quick check we could perform is by
using the `instanceof` operator. The `instanceof` operator checks to see whether
an object is an instance of a particular class or not and will return a boolean
value. If it evaluates to true, then the object is an instance of the class.

```java
Animal myAnimal = new Cat();

if (myAnimal instanceof Cat) {
    System.out.println("I'm a cat!");
} else if (myAnimal instanceof Animal) {
    System.out.println("I'm an animal!");
} else {
    System.out.println("I'm something else!");
}
```

This produces the following output:

```plaintext
I'm a cat!
```

As we can see, `myAnimal` object is an object of type `Cat`, not of type
`Animal` That's because there is a difference between the type at compile-time
and the type at runtime. At compile time, meaning when the code was written, the
type of the `myAnimal` variable was defined as `Animal`. However, when the
program runs, a `Cat` data type is assigned to the `myAnimal` variable instead. So
the runtime type of `myAnimal` is `Cat` and this is valid through upcasting, as
we saw above.

In order to downcast, we will need to explicitly cast the object
like this:

```java
public class DowncastExample {
    public static void main(String[] args) {
        Animal myAnimal;
        myAnimal = new Cat();  //implicit upcast

        myAnimal.setAge(5);
        System.out.println(myAnimal.getAge());

        Cat myCat = (Cat)myAnimal; //explicit downcast
        myCat.purr();
    }
}
```

Note that the example above will compile and run because the runtime type of
`myAnimal` is `Cat`, which is compatible with the `myCat` variable. In other
words, in this case, the animal really was a cat!

But if we were to do something like this:

```java
import java.util.Random;

public class DowncastExample {

    public static void main(String[] args) {
        Animal myAnimal;
        Random rand = new Random();
        if (rand.nextBoolean()) {
            myAnimal = new Cat();
        }
        else {
            myAnimal = new Animal();
        }

        myAnimal.setAge(5);
        System.out.println(myAnimal.getAge());

        Cat myCat = (Cat)myAnimal; //may throw a ClassCastException
        myCat.purr();  //may thro
    }
}

```


We might get a `ClassCastException` thrown when we run the code. 

If `myAnimal` references an instance of `Cat`, the program prints:

```text
5
purr purr purr
```

But if `myAnimal` references an instance of `Animal`, the code
`Cat myCat = (Cat)myAnimal` causes a `ClassCastException`:

```text
5
Exception in thread "main" java.lang.ClassCastException: 
class Animal cannot be cast to class Cat
	at DowncastExample.main(DowncastExample.java:18)

```

The exception prevents the program from continuing to the next line,
which calls the `purr()` method and not all `Animal` objects know how to purr.

