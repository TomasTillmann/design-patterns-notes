# **Creational Patterns**
# Prototype
* want to make exact copy of some object x instantiated from class X
  * make a new intance y of X
  * fill all fields of y the same as x have

* can be done in client code, but sometimes not possible
  * either client doesnt have access to X (x was given by some library)
  * or some fields of x are private
 
 ## Solution
 * define new interface Prototype (or IClonable) and define method Clone()
 * make X implement Prototype
 * imlement Clone()
 
 ## How to implement Clone()
 ```c#
 class X {
 public a;
 private b;
 ...
 
  public X(){
  // used normally
  }
  
  public X(X instanceToClone){
  // used for cloning
  // can be either public or private
  this.a = instanceToClone.a;
  this.b = instanceToClone.b;
  ...
  }
  
  Clone(){
    return new X(this);
  }
 }
 ```
 
## Advantages
* Client doesnt need to care how clone works - doesnt need to take care about implementation details
* cloning of object is independent from concrete class
    * client doesnt need to know anything about the class, just calls `newX = x.Clone();`
* much easier for client to copy complex objects

## Disadvantages
* complex objects with eg. circular dependencies might be too difficult to copy


# Singleton
## What it is
* only one instance of a class
* ensures global access

## When to use
* One shared something over the whole programm for the whole time
 * eg. database, some file

## Implementation
```cs
public class Singleton{
 public a;
 public b;
 ...
 
 private static Singleton instance;
 private Singleton(){}
 
 public static Singleton GetInstance(){
 if(instance == null) { instance = new Singleton() }
 return instance;
}
...
```
* private constructor
* static instance
* method GetInstance

## Advantages
* one instance guaranteed - global access to one resource, no duplicates
* more convinient than static class
* globals are in some kind of container, not scattered all around the place

## Disadvantages
* different modules sharing one singleton might know too much about each other
* violes single responsibility principle - "solves" two thing
* very hard to implement when using parallel programming - very hard to ensure no data races occur (and then potentially deadlocks)
* hard to write tests

# **Behavioral patterns**
