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
## Use
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
 * 
  ## Other use
 * got some object x from some library and the library doesn't want client to have access to it's class (cannot call new X());
 * Library hence makes x implement Prototype, so client can make copy of x
 * eg Unity has fabrics, cannot call new Fabric(), client just makes copy of it (bullets, troops, npcs, ... )
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
 * its not a global variable

## When to use
* One shared something over the whole programm for the whole time
 * eg. database, some file, one logger, ...

## Difference between static
* live of static class cannot be controlled
* singleton can be passed around
* singleton can inherit
* singleton can use lazy algorithms

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
* possibility of resource leaks
  * when to unallocate? Let it unallocate at the end of process?

# **Structural patterns**

# Adapter
* don't confuse with wrapper (that is decorator)
* converts one API to another API
* convinient, when we have some common API but we want to use 3rd party API
  * instead of learning this new API and working with it in client code, we create adapter that translates our common API paradigms to this 3rd library API
* real life analogy: electrical plug in US and in Europe

## Actors
1. Adapter
 * converts from one API to another
2. Service
  * usually some 3rd party API
  * adapter converts from current bussines logic API's paradigms to this 3rd party API
3. client interface
  * what we want our service incorporate into

## Implementation
* adapter inherits from client interface
* hence, it has now the same methods
* these methods imitate the behaviour of service

```cs
public class Service{
 public int getWidth(){
 }
}

public class A : ClientInterface {
public int getRadius(){
 }
}

public class B : ClientInterface {
  public int getRadius(){
  }
}
...

// object adapter
public class AdapterOfService : ClientInterface {
  private Service service;

  public AdapterOfService(Service s){
    service = s;
  }

  public int getRadius(){
     return service.getWidth();
     // or perform some calculation or something;
  }
}

// class adapter
// or adapter can inherit from service
public class AdapterOfService : ClientInterface, /*(private)*/ Service {
  public int getRadius(){
    return getWidth();
    // can simply call Services methods, doesnt need to remember Service instance
  }
}
```
* adapter cal also define some methods from client interface without the use of service

## pluggable adapter
```cs
// Service
public class Cat{
  public void Meow(){ WriteLine("Meow"); }
}

// Service
public class Dog{
  public void Bark(){ WriteLine("Meow"); }
}

// client interface
public interface SoundMaker{
  public void MakeSound();
}

// pluggable adapter
public class AnimalSound : SoundMaker{
  public void Action MakeSound();
  public AnimalSound(Action MakeSound){
    this.MakeSound = MakeSound;
  }
}

//client code
SoundMaker dogSound = new AnimalSound(new Dog().Bark);
SoundMaker catSound = new AnimalSound(new Cat().Meow);


```
## When to use
* client wants to use non compatible interface
* dynamic change of objects behaviour (wrapper)

## Advantages
* single responsibiity principle
   * seperates services API from the bussiness logic (separation of concerns)
* open closed principle
   * can add new adapters easily

## Disadvantages
* complexity of code can increase dramatically, because of new adpater classes, potentially adpter interfaces (if more than one)
   * might be easier to just rewrite service API

# Bridge
* seperates abstraction and implementation
* assigns responsibility for each dimension to each new module (class)

## Actors
* abstraction
* implementation

## Example
* we have enemy
* enemy has different weapons (sword, bow, hands, ...)
* class enemy
  * subclasses - swordEnemy, bowEnemy, ...
* but later we want some enemies to have different appereances (skeleton, viking, ...)
  * skeletonSwordEnemy, skeletonBowEnemy, ..., vikingSwordEnemy, ... ?? 
    * exponential growth
* it's better to seperate these two dimensions

```cs
// abstraction
public class Enemy{
  private IWeapon _weapon;
  private IAppereance _appereance;

  public Enemy(IWeapon weapon, IAppereance appereance){
    _weapon = weapon; _appereance = appereance;
  }

  public void Attack(Player player){
    // delegates work of attacking to a different entity - seperation
    _weapon.Attack(player);
  }

  public void OnLoad(){
    // delegates work of attacking to a different entity - seperation
    _appereance.Render(this);
  }
}

// implementation interface
public interface IWeapon{
  public void Attack(Player player);
  ...
}

// implementation interface
public interface IAppereance{
  public void Render(Enemy enemy);
  ...
}

public Sword : IWeapon {
  public void Attack(Player player) {
    // concrete implementation
  }
}
...
```

* this way we avoid exponential growth and bring seperation and abstraction, hence bringing more maintainable and easily understandable code
* it also brigns flexibility, because abstraction isn't dependent on implementation

* another nice example is for example some algorithm (find shortest path in maze)
  * abstraction is usually the same (find nearby cells, then check if we were there before etc ...)
  * what differs is how we choose the best next move (moves) where to go in each step
    * this is implementation
    * we can seperate abstraction (the flow of the algorithm) and implementation (concrecete calculation and decision making based on some data)

## When to use
* good for breaking up one big monolithic class
* or potentially exponentially large subclasses system

## Advantages
* seperation of concerns - single responsibility principle
* open/closed principle
  * easily can add and change implementations, even abstractions
    * can make more extended abstractions, using the same or new implementations
* client can work in abstraction layer, which is very convinient

## Disadvantages
* I see no disadvantages

# Decorator
* also called wrapper
* uses composition instead of inheritance
* let's client change behaviour of objects at run time

## Example
* Notification application
* class notification
* want to add support for different communication channels
* subclasses of notification: sms, email, call, ...
* what if we want aplication object with not only one communication channel but more?

## Bad solutions
1. add new subclasses: smsEmail, smsCall, ...
  * terrible, exponential growth
2. one super class, fields will be set in constructor accordingly
  * bool isSms; bool isEmail; ....
  * hard to work with on client code side

## Great solution
* use decorator

## Actors
* baseDecorator
* concreteDecorators
* component
* concreteComponent

```cs
// component
public interface Notification {
  public void Notify();
}

// concrete component
// I chosed email as default communication channel
public class Email : Notification {
  private string _mail;
  public Email(string mail) {
    _mail = mail;
  }

  public void Notify() {
    // concrete implementation of notification via email
  }
}

// base decorator - it seems to me as optional but it adds consistency
public abstract class BaseDecorator : Notification {
  private Notification _wrappee;
  public BaseDecorator(Notification wrappee) {
    _wrappee = wrappee;
  }

  public virtual void Notify();
}

// concrete decorator
public abstract class SmsDecorator : BaseDecorator {
  private Notification _wrappee;
  public SmsDecorator(Notification wrappee) {
    _wrappee = wrappee;
  }

  public Notify() {
    // implementation of notification via SMS
    _wrappee.Notify();
  }
}

// concrete decorator
public abstract class CallDecorator : BaseDecorator {
  private Notification _wrappee;
  public CallDecorator(Notification wrappee) {
    _wrappee = wrappee;
  }

  public Notify() {
    // implementation of notification via phone
    _wrappee.Notify();
  }
}

// client code
Notification notification = new Email();
notification = new SmsDecorator(notification);
notification = new CallDecorator(notification);

notification.Notify();
// CallDecorator instance calls the customer and then delegates the work to SmsDecorator instance
// and so on


// call -> sms -> email
```

## When to use
* want to add extra behaviour at runtime to object
* use when inheritance is awkward to use

## Advantages
* single responsibility principle
* add more functionallity to object at run time
* no need for exponentially many subclasses

## Disadvantages
* hard to delete decorator from stack
* the ordering of decorators matters
* 

# Facade
* provides simpler communication layer between client and complext system/API/library ...
* provides only functionality needed by the client
* hides implementation details about how this complex system works
* layer between clients and subsystem

## Example
* Compiler
  * takes high level orders from client
  * delegates them to subsystems
    * scanner
    * parser
* scanner and parser can too have be implemented as facade
  * multi level facade hierarchy

## When to not use
* facade has the same complexity as underlying system
* not needed at all, communication with system isn't that complex
* too much bussiness logic in the facade
  * facade should only act as kind of a middleman between clients and system
  * shouldn't make it's own logic
* facade should be an option, not a necessity
  * clients should be able to communicate with system directly with the same effects
  * communicating through facade is just more convinient
* system shouldn't know about facade
  * one system can also have multiple facades, each "converting" system's API to each client's needs

## Advantages
* easier interface, taylored to clients needs
* open/closed principle
  * can easily add or delete facades

## Disadvantages
* client code can be too coupled to the facade - become a god object

# Flyweigth
* detaches shared state between many objects
* thanks to that, it reduces memory consumption

## Example
* game with many ants
```cs
public class Ant {
  public Location location;
  public int health;
  public Sprite sprite;
  public Color color;

  public Ant(Location location, Health health, Sprite sprite, Color color) {
    this.location = location;
    this.health = health;
    this.type = type;
    this.sprite = sprite;
    this.color = color;
  }

  public void Move(Location location) {
    this.location = location;
  }
}
```
* having many ants will be very hard on memory

## Solution
```cs
// specific mutable data - intrinsic
public class Ant {
  public Location location;
  public int health;
  public AntType type;      // reference to flyweight

  public Ant(Location location, Health health, Type type) {
    this.location = location;
    this.health = health;
    this.type = type;
  }

  // methods stay the same
  public void Move(Location location) {
    this.location = location;
  }
}


// shared immutable data - extrinsic
public class AntType {
  public Sprite sprite;
  public Color color;
}


// Ant can be a struct, to reduce class overhead
public struct Ant {
  public Location location;
  public int health;
  public AntType type;

  ...
}

// shared data shouldn't be struct, because it needs to live on heap, in order to be shared
```

* bigger the extrinsic data, bigger the memory save
* it might be hard to manage flyweights just like that, imagine client code

```cs
// this is wrong, cannot be creating new instances of type each time we want an ant
Ant ant = new Ant(new Location(1,2), 100, new Type(SpriteEnum.Warrior, Color.Red));
```
* we nned to recycle extrinsic data, and we don't want client to do it

## Flyweight factory
* when client wants new Ants, he asks ant factory

```cs
public class AntFactory {
  private AntType[] types;

  public GetAnt(Location location, int health, Sprite sprite, Color color) {
    return new Ant(location, health, GetAntType(sprite, color));
  } 

  // recycles already initiated shared types
  private AntType GetAntType(sprite, color) {
    AntType type = types.Find(sprite, color);
    return (type == null) ? new AntType(sprite, color) : type;
  }
}

//client
var factory = new AntFactory();
var rand = new AntRandom();
var ants = new List<Ant>();

for(int i = 0; i < 100000; i++) {
  ants.Add(factory.GetAnt(rand.Location(), rand.Health(), rand.Type()));
}
```
* client doesn't care at all about implementation

## Actors
* Flyweight
  * shared state
  * ant type

* Context
  * unique state with reference to flyweight
    * Ant

* Flyweight Factory
  * manages flyweights
  * client communicates with it

## It still could be done better
```cs
public struct Ant {
  // intrinsic state
  public Location location;
  public int health;

  // no reference to shared state

  public Ant(Location location, Health health) {
    this.location = location;
    this.health = health;
  }

  // passing the flyweight in when needed
  public void Attack(Player player, AntType type) {

    // how ant attacks is dermined by type and currents ants health
    // extrinsic and intrinsic state
    type.Attack(player, health);
  }

  // some methods might remain the same becaue they don't need flyweight
  public void Move(Location location) {
    this.location = location;
  }
}


// shared immutable data - extrinsic
public class AntType {
  public Sprite sprite;
  public Color color;

}

public class Warrior : AntType {
  public void Attack(Player player, int health) {
    // concrete attack of warrior based on health implementation
  }
}


// Ant can be a struct, to reduce class overhead
public struct Ant {
  public Location location;
  public int health;
  public AntType type;

  ...
}
```
* this way, we saved even space for all the references
* client decides at runtime what ant type will he pass in to each ant

## When to use
* only when we really need to save space 


## Advantages
* huge RAM space saver

## Disadvantages
* RAM over CPU trade  
  * possible recalculation everytime flyweight method is called
* big complications in the code and a lot of new problems to take care of
  * shared state pool
  * allocation and deallocation of objects
    * eg client should tell flyweightFactory, when hes not going to use some shared state anymore

# Proxy
* acts as a middleman between some service and client
* its very useful if we want to make some operations before or/and after we call some service methods

## Example
* Client communicates with databaseObject, but client want to have cache for this database
* this databaseObject is 3rd party so he cannot change it's code

## Solution
* creates a proxy, that implements the same interface as databaseObject, disguising as the database itself
* proxy has reference od database
* proxy can add some new logic and the functionality of databseObject remains the same
```cs
public interface Database {
  public Data getCount(Entity entity);
  ...

}
public class DatabaseObject : Database {
  public Data getCount(Entity entity) {
    // we don't know the implementation, and we don't have to
  }
}


public class DatabseProxy : Database {
  // reference to original database
  private Database database;
  // internal cache
  private Dict<Entity, Data> cache; 

  public DatabaseProxy(Database database) {
    this.database = database;
  }
  
  // wrapped cache around original call to database
  public int getCount(Entity entity) {
    if(cache.Contains(entity)) {
      return cache[entity];
    }
    else {
      Data data = database.getCount(entity);
      cache[entity] = data;
      return data;
    }
  }
}
```

* proxy used above is surprisingly called cache proxy
* there are other proxies

## Types of proxies
* protection proxy
  * protects the original resource
  * eg. wraps age check around Drive()

* remote proxy
  * local representant of remote object

* virtual proxy
  * mock object representing the real one
  * eg we need to test some module that uses something that isnt implemented yet

* cache proxy
  * as seen above

* logging proxy
  * logging

## Advantages
* optmization and strategy of accessing some resources
* seperation of concerns / single responsibility principle
  * seperation of eg administration (protection proxy, logging proxy) or optimization (cache proxy) from functional code 
* client doesnt need to know about anything of this, he can be completely oblivious to this new added layer
* open/closed principle
  * new proxies can be removed and/or added very easily 

## Disadvantages
* makes code a lot more complex
* adds new layer
  



