# **Creational Patterns**
# Builder
## Problem
* we want to construct some complex object
 * eg. lots of different parametrizations of fields

## Bad solutions
1. one huge constructor
 * not clean, messy, hard to navigate
2. many sublasses, each defining one concrete complex object
  * huge space of possibilities -> potentially enormous number of subclasses
3. everything public, what is not set specifically after inicialization by client has some default value
  * breaks encapsulation

## Good solution
* use builder

## Actors
1. Builder
 * defines step how to build a product
 * interface or AC
2. ConcreteBuilder
 * implements concrete steps accordingly
3. Director
 * makes more buildres actions as one
 * more convinient for the client
4. Product
 * what is beining build


 
## Imlementation
* lets say we, the complex object is house
```cs
public class House{
//trililion fields
 public House(... // trillion paramters){
 }
}

public interface HouseBuilder {
public void BuildDoor();
...
return this;  // allows nice fluent syntax eg. builder.BuildDoor().BuildWindows().BuildRoof();
}

public class ElegantHouseBuilder : HouseBuilder{
public house;
public HouseBuilder(House houseToBuildStepByStep){
 house = houseToBuildStepByStep;
}

// define methods that build house step by step

// we can either parametrize builders step by step methods, or paramatrize builder in constructor (e.g. we want a builder that builds houses only from wood)
// this specific builder construct only elegant houses

// builds elengant door from given material
public void BuildDoor(Material material){
 house.door = new Door(material);
}
...
}

// we can have other builders
// building terrible houses for example
public class TerribleHouseBuilder : HouseBuilder {
 public TerribleHouseBuilder(Material material, ...){
 ... //further paramterizing in constructor
 // if parametrization too complex, it might be a good idea to use builders for builders .........
 }
}
```
* this still might be too exhaustive for client code, especially if there are steps that are done together most of the time
* Solution is to also use a director

```cs
public class HouseDirector{
 private HouseBuilder builder;
 public HouseDirector(HouseBuilder builder){
  this.builder = builder;
 }
 
 public void buildGarden(){
  builder.fence();
  builder.grassSpace();
  builder.fountain();
  builder.Chairs();
  ......
 }
}
```
## Advantages
* gets rid of huge constructors
 * easier client code, more maintainable
 * doesnt need to know details of implementation when not needed (when just some basic instance is needed)
* same steps to produce different things
  * differs eg houses that differ only in material/style/size/price ... , cars, books etc ...
* Single responsibility principle - isolate complex building process from actual bussiness logic
* when creating tree like structures (eg some subtree in composite tree)
* encapsulation, hides implementation, like concrete product's properties (can only be modified by builders build methods)

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

# **Structural patterns**

