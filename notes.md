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


 
## Implementation
* lets say we, the complex object is house
```cs
public class House {
//trililion fields
  public House(... // trillion paramters){
  }
}

public interface HouseBuilder {
public void BuildDoor();
...
}

public class ElegantHouseBuilder : HouseBuilder {
public house;

public ElegantHouseBuilder(House houseToBuildStepByStep){
  house = houseToBuildStepByStep;
}

// define methods that build house step by step

// we can either parametrize builders step by step methods, or paramatrize builder in constructor (e.g. we want a builder that builds houses only from wood)
// this specific builder construct only elegant houses

// builds elengant door from given material
public void BuildDoor(Material material){
  house.door = new Door(material);

    return this;  // allows nice fluent syntax eg. builder.BuildDoor().BuildWindows().BuildRoof();
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

## Relation to other patterns
* abstract factory / factory method
  * returns product immediately
  * builder builds it step by step

* Composite
  * builder can build some parts of composite tree, becuase builders construction steps can be programmed recursively

* Bridge
  * director acts like abstraction
  * builder acts like implementation

* singleton
  * can be implemented as singleton

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

## Relation to other patterns
* Abstract factory
  * factory methods can be implemented with prototypes

* Command
  * when you want to save commands to history

* Composite, decorator
  * cloning of complex objects

* memento
  * prototype can be used as mememnto
  * originator is protoptype - has clone()  
  * state to remember (= memento) is clone of originator 



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

## Relations
* Facade
  * one instance of facade to some complex subsystem is usually enough

* Flyweight
  * if only one shared state
  * flyweight object is immutable though


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
  
## Relations
* Bridge
  * bridge is designed up front
  * adapter is designed with already well functioning existing app

* Facade
  * simpler interface for usually many complex subsystems
  * adapter changes interface to client interface, usually of one object

* Proxy
  * same interface, doing something different than underlying object

* Decorator
  * same interface with enhanced functionality
  * supports recursive composition


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

## Relations
* adapter
  * bridge is up front

* builder
  * director is abstraction and concrete builders are implementations

* state, decorator, proxy, adapter, ...
  * all delegate work to other objects but each of them do it with different purpose

* abstract factory / factory method
  * create concrete objects with concrete implementations
  * hides complexity from client
  * eg: factory method: CreateEnemy(Weapon.Sword);
  * abstract factory: factory for enemies
    * method CreateWithSword();

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

## Relations
* adapter

* proxy

* chain of responsibility
  * same structure
  * differences:
    * CoR each chain element does some calculation independently of each other
    * can stop the request at any point
    * Decorator keeps the same interface
    * can only add functionality, cannot stop the request at any point
  
* composite
  * decorator has only one child
  * composite just cares about the structure and calculation happens in children
  * decorator has some functionality at each step
  * can work together

* Prototype
  * easier for copying complex recursive decoratrs structure
  * only way how to make the same instance of complex decorator

* Strategy

* Proxy
  * same composition structure and both delegate work on underlying object
  * proxy delegates work with underlying object on itself, client doesnt even need to know that he is communicating with proxy and not the service itself
  * decorats on the other hand are managed by client


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

## Relations
* Adapter

* Abstract factory
  * if we only want to hind how complex entities are created, abstract factory can work as a facade

* Proxy
  * both provide some interface to object
  * proxy the same
  * facade easier

* Mediator
  * both provide communication channel between complex systems to avoid tight coupling
  * facade is optional, client can communicate directly with subsystem
  * subsystem is unaware of the facade
  * objects know only about mediator, cannot communicate with each other directly

* singleton
  * usually one instance of facade is necessary to some complex system (resource)

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

## Relations
* Composite
  * composite leafs can be shared - flyweight

* Signleton
  * if only one shared state
  * singleton can be mutable though

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

## Relations
* adapter

* facade

* decorator
  
# **Behavioral patterns**
# Chain of responsibility
* structure to deal with clients requests
* objects composed to a chain like struture
* each object in this chain either fullfils the request or pass the request along in the chain

## Example
* request to process different coins (eg sw for snack dispenser)

## Bad solutions
1. one huge monolithic class with swith over coin
1. one interface Handler and many subclasses as coin handlers (eg oneCoinHandler, twoCoinHandler, ...)
  * client needs to know the implementation
  * needs to try every handler and see if it succeded or not
  * that is extremely inconvinient API

## Great solution
* use chaing of responsibility
* each handler has successor
* each handler will try to execute or pass the request along the chain, to it's successor

## Actors
1. Handler
* interface for each handler in the chain
  * Execute() (SetSuccessor())

2. BaseHandler
* optional abstract class
* defines reference to successor and some common behaviour
* see code for example

3. concreteHandler
* concrete handler implementation

```cs
// handler interface
public interface Handler {
  public void Execute(Coin coin);
}

// optional
//defines some common behaviour
public abstract class BaseCoinHandler {
  public virtual Handler Successor;

  public virtual void Execute(Coin coin) {
    // coin is the required size 
    if(coin < 42) {
      // process coin
    }
    else if (Successor != null) {
      Successor.Execute();
    }
  }
}

public oneCoinHandler : BaseCoinHandler {
  public override Handler Successor;


  public override void Execute(Coin coin) {
    // one coin
    if (coin.Size <= 2) {
      // execute 
    }
    else if(Successor != null) {
      Successor.Execute();
    }
  }
}

// client code
//init of handlers - should be hidden in some method - it would make sense to create new class coinHandler : Handler with method getNewCoinHandler or smthng and then call Execute
Handler c1 = new oneCoinHandler(); 
Handler c2 = new twoCoinHandler();
...
Handler c50 = new fiftyCoinHandler();

c50.Successor = c20;
c20.Successor = c10;
...

Handler coinHandler = c50;

// actual code
Coin coin = Dispenser.GetCoin();
coinHandler.Execute(coin);
```

* client doesnt need to know about any implementation details
* no for loops or anything like that needed
* what if

```cs
coinHandler.Execute(42);
```
* unknown coin, it will be passed to the last handler, whose successor is null
* How should handler behave?
* Lets create new default handler that will finish the chain

```cs
public class DefaultCoinHandler : BaseCoinHandler {
  public Handler Successor = null;

  public override void Execute(Coin coin) {
    // do some default operation
  } 
}
Handler defaultHandler =  new DefaultCoinHandler();
c1.Successor = defaultHandler;
```

## When to use
* client wants to have some request processed and he doesn't care how it's done
* many different conditions when proccessing a request are needed
  * essentially need for composition instead of static class subclassing
* when order of processing matters

## Advantages
* control of requests processing
* flexibly can change requests
* open/closed principle
  * add, change and delete chains dynamically without breaking client code

## Disadvantages
* some request might become unhandled

## Relations
* chain of responsibility, command, mediator, observer
  * from the perspective of sender and receiver
  * chain of responsibility doesnt care who he sends a request or whether he sends it, most important thing is to complete the request
  * command seperates sender and receiver, but he exactly knows who the request handles
  * mediator is in between every communication between true senders and receivers
  * observer
    * publisher doesnt care who he sends the request
    * receivers care who they subscribe to and they can change it dynamically (on client side)
  
* Composite
  * not only one chain but a tree like structure

* Decorator
  * same as in decorator already

# Command
* zapouzdreni vykonani konkretni akce (metody na nejakem objektu misto konkretniho objektu) do objektu

## Actors
1. **ICommand**
* Execute()

2. **ConcreteCommand : ICommand**
* implementuje Execute() 
* zapouzdruje receivra
  * odkaz na objekt s tou metodou kterou chceme volat
* pamatuje si parametry s jakymi chceme volat
* ConcreteCommand pouze deleguje akci, neprovadi zadny konkretni vypocet

3. **Invoker**
* rozhoduje, kdy se provede Execute()
* ma referenci na ConcreteCommand, pouze provede `concreteCommand.Execute()`

4. **Client**
* inicializuje Invokera s konkretnimi commands

## Example
### GUI
* cudliky jsou Invokeri
* akce co se ma provest po kliknuti cudliku je command

### Light switch
```cs
// what command operates on
class Light
{
    void TurnOn() {
        WriteLine("Light on");
    }

    void TurnOff() {
        WriteLine("Light off");
    }
}

// Invoker
class Switch
{
    private ICommand upCommand, downCommand;

    public Switch(ICommand up, ICommand down)    
    {
        upCommand = up;
        downCommand = down;
    }

    public void FlipUp() {
        upCommand.Execute();
    }

    public void FlipDown() {
        downCommand.Execute();
    }
}

public interface ICommand
{
    void Execute();
}


class LightOnCommand : ICommand
{
    private Light myLight;

    LightOnCommand(Light light) {
        myLight = light;
    }

    void Execute() {
        myLight.TurnOn();
    }
}

class LightOffCommand : ICommand
{
    private Light myLight;

    LightOffCommand(Light light) {
        myLight = light;
    }

    public void Execute() {
        myLight.TurnOff();
    }
}

// Client
static void Main(string[] args)
{
    Light light = new Light();

    ICommand lightOn  = new LightOnCommand(light);
    ICommand lightOff = new LightOffCommand(light);

    Switch mySwitch = new Switch(lightOn, lightOff);
    mySwitch.FlipUp();
    mySwitch.FlipDown();
}
```

### Undo redo

```cs
interface ICommand
{
    void Execute();
    void Undo();
}

class PasteCommand : ICommand
{
    private Document document;
    private Stack<string> history;

    public PasteCommand(Document doc)
    {
        document = doc;
        
        // shared history between multiple commands
        history = document.GetHistory();
    }

    void Execute()
    {
        // save old state
        history.Push(document.GetText());
        
        // changes to new state
        document.Paste();
    }

    void Undo()
    {
        document.SetText(history.Pop(oldText));
    }
}

// client

// pasteButton is invoker
// invokes command whenever it's clicked
pasteButton.AddEventListener(new PasteCommand(this.document), "click");

```
### Modification
* Instead of command objects, lambdas/delegates could be used

## When to use
* Action is now a standaolne object, hence you can pass commands as method arguments, switch commands at run time, store commands for later ...

1. store, pass, change action at run time
2. undo, redo
3. deffered excution of operation 

## Advantages
* separation of concerns
  * caller and callee is seperated
  * in favor of single responsibility principle
* open/closed principle
  * can easily add new commands

## Disadvantages
* each command is new subclass -> potentially big number of commands

## Relations
* sender receiver analogy (same as in CoR)

* CoR
  * each handler can be also a command
  * this way, we can pass CoR in some invoker and it acts as a command

* memento
  * both can be used to implement undo operation on some target object
  * memento just saves the target state and then someone else executes the operation
  * command can save the state and than also execute

* strategy
  * both do the same thing but with different intents
  * command is used for storing some actions for later (as objects), even in multiple context
  * strategy allows for swapping of implememntations of abstractions, usually in same context
    * just describes different ways of doing the same thing
  
* prototype
  * saving commands to history


# Iterator
* iterates over a collection
* hides internal representation of data
* client doesn't need to know how data are stored, client just cares about how to iterate over them, in order to access to them

## Actors
1. abstract list
2. concrete list
3. abstract iterator
4. concrete iterator

## Types of iterators
### Who is responsible for the iteration
1. External
* client manages the access
```cs
var iterator = collection.GetEnumerator();
while(nextElement = iterator.Next() != null) {
  // execution
}
```

2. Internal
* iterator manages the access
```cs
collection.ForEach() {
  // execution
}
```

## Who implements the algorithm
1. Cursor
* only  remembers index where it is in the collection
* public API is sufficient

2. Iterator
* needs to have access on implememntation of the collection
  * public API isn't sufficient
* C#
  * private nested class
* C++
  * friend class


## Virtual iterators
* iterates over infinity collection
  * eg fibonnachi numbers
* lazy iterations - generates data while iterating
  * IEnumerable in C# (eg LINQ)

## What to do when collection changed while iterating over it
* C# / Java - exception
* C - undefined behavior

## Advantages
* One collection can have more iterators independently of one another
* No need for the client to care about internal representation
* easy interface for collection sequential accesss

# Mediator
* many objects that communicate with each other -> mess
* one object responsible only for communication between them

## Example
* plane coordination -> one coordination tower = mediator

## Actors
1. Mediator interface
2. Concrete mediator
3. Components interface
4. Concrete components

```cs
public inerface IMediator {
  public void Notify(object sender, Event event);
}

public interfce IComponent {
  public void Operation();
}

public class Mediator : IMediator {
  public void Notify(object sender, Event event) {
    if (sender is ComponentA) {
      switch(event) {
        ...
      }
    }

    if(sender is ComponentB) {
      ...
    }
  }
}

public class ComponentA : IComponent {
  private Mediator mediator;

  public Component(Mediator mediator) {
    this.mediator = mediator;
  }

  public void Operation() {
    // some operation 


    // notifies other components 
    mediator.Notify(this, Events.SomeEvent);
  }
}

public class ComponentB : IComponent {
  ...
}

```
## How to notify concrete component?
* mediator notifies all components that current component is ever communicating with
* the receiver decides, whether the event is important or not

## Advantages
* separation of concerns
  * mediator / components

* loose coupling

* open closed principlie
  * can easily add new components and new reactions on events

## Disadvantages
* god object potential

## Relations
* composite
  * easier traversal

* factory method
  * can create iterators for collections in concrete subclasses

* memento
  * save states while iterating over collection, allows for possible rollback

* visitor
  * traverse complex structure (eg graph of map) and operate on each object (eg each node of the graph)

# Memento
* use when we want to save state and mayble later come back and restore this state

## Example
* house with lights
* we light up different lights, than we want to save this state, we light up/light off some lights and now we want to return to the saved state

### Solution 1
* inner state of house public
* breaks encapsulation

```cs
public class House {
  // should be private
  public Light[] lights;
  ...
  public void LightUp(int index) {
    lights[index] = lights[index].lightUp();
  }
...
}

// client

house.LighUp(2);
house.LighUp(1);
house.LighUp(5);

// save state
Light[] lights = house.lights;

...

house.lights = lights;
```

* breaks encapsulation - really bad

## Solution 2
```cs
public class House {
  private Light[] lights;
  ...
  public void LightUp(int index) {
    lights[index] = lights[index].lightUp();
  }
...

  public void SaveState() {
    ...
  }

  public void RestoreState() {
    ...
  }

}
```
* breaks single responsibility principle
* House shouldn't change his internals - sepearation of concerns 

## Solution 3
* use memento

## Actors
1. Originator
* House
* Entity we want to manage states for

2. Memento
* state

3. Care taker
  * manages states lifecycle
  * that's all

## Bad Example - probably will delete
```cs
public class CareTaker {
  private Dict<Key, Memento> mementos;
  private House house;

  public CareTaker(House house) {
    this.house = house;
  }

  public void AddMemento(Key key) {
    mementos[key] = house.CreateMemento(memento);
  }

  public void SetMemento(Key key) {
    house.SetMemento(mementos[key]);
  }
}

public class House {
  ...

  private Light[] lights;

  public void CreateMemento() {
    return new Memento(lights);
  }

  public void SetMemento(Key key) {
    lights = mementos[key].State;
  }
}

public class Memento {
  private Light[] State;
  public Memento(Light[] state) {
    this.state = state;
  }
}

// client
House house = new House();
CareTaker taker = new CareTaker(house);

house.LighUp(2);
house.LightUp(5);
house.LightOff(3);

Key state1 = new Key("state 1");
taker.AddMemento(state1);

house.LighUp(1);
house.LightOff(3);
house.LightUp(5);
...

// restore nicely
taker.SetMemento(state1);
```

## Example
* undo redo of comlex GUI app

### Bad solution 
* remember state of whole application between every actions
  * a lot of space required
  * cant access some objects data, cant be stored for later

### Good solution 
* each object takes care only of his own states - mementos
* pushes these mementos on "global" stack

## Actors
1. Originators
* entities whose state is remembered

2. Memento
* some entity state

3. Care taker
* manages mementos life cycle
* nothing more!

```cs
// gui elements

public Editor { 
  // some data - state
  private int x;
  private int y;
  ...

  // some actions
  ...

  public Memento CreateMemento() {
    return new Memento(x, y, ... );
  }

  public void Restore(Memento memento) {
    this.x = memento.x;
    this.y = memento.y;
    ...
  }

  // nested - origiantor can see everything, but care taker can only manage their lifecycle (not eg data, or create new ones)

  // its also immutable - might be also a struct
  private class Memento {
    private int x;
    private int y;
    ...

    public Memento(int x, int y, ...) {
      this.x = x; this.y = y;
      ...
    }
  }
}

public class CareTaker {

  // history of the editor
  private Stack<Memento> mementos;
  private Editor editor;

  public CareTaker(Editor editor) {
    this.editor = editor;
  }

  // can be called anytime before operation
  public void MakeBackup() {
    mementos.Push(new Memento(editor.x, editor.y, ...));
  }

  public void Undo() {
    editor.restore(mementos.Pop());
  }
}
```

## When to use
* when yo want to restore states
* when other implementations of this would violate encapsulation

## Advantages
* simpler originators code, care taker manages some of it
* you can produce snapshots

## Disadvantages
* if too many mementos too often, it could be hard on memory
* nested classing might be a problem

## Relations
* command
  * undo

* iterator
  * capture state of iterator

* prototype
  * same as in prototype

# Observer
* some object want to be notified, when change in some other happens, in order to make some action

## Actors
1. Publisher / Subject
2. Subscriber

## Example
* meteostation and display
* display wants to change its data whenever meteostation changes temperature
* could be solved by active waiting, but that is inefficient

## Good solution
* meteostation is publisher
* we attach display as one of its subscribers
* whenever change in meteostation occurs, it notifies all of its subscribers

```cs
// subscribers interface
public interface Sub {
  public void Execute();
}

public interface IPublisher {
  public void Notify();
  public void Attach();
  public void Detach();

  // optional
  public void Logic();
}

public class meteostation : IPublisher {
  private int temp;
  private List<Sub> subs;

  public void ChangeTemp(int newTemp) {
    temp = newTemp;

    //notify subscribers
    Notify();
  }

  public void Attach(Sub sub) {
    subs.Add(sub);
  }

  public void Detach(Sub sub) {
    subs.Delete(sub);
  }

  // passive waiting
  public void Notify() {
    foreach(var sub in subs) {
      sub.Execute();
    }
  }
}
```

* if more subscribers, not really related, they can still implement sub interface and Execute will just call some different sub method

## How to pass data to subs?
### Push
* publisher pushes data to osberver
* needs to distinguish between them though
* wider interface - overload methods

```cs
  public void Notify() {
    foreach(var sub in subs) {
      if(sub is Display)
        sub.Execute(temp);
    }
  }
```

### Pull
* better imo

```cs
  // remains the same
  public void Notify() {
    foreach(var sub in subs) {
        sub.Execute(temp);
    }
  }


  // sub
  public void Execute() {
    int temp = meteostation.GetState();
  }
```
* only subs that want some data will ask for them
* required fields needs to be public though (on publisher)


## When to use
* one object changes something when another one does too, isnt known before hand (run time decision)
  * eg gui button should do some action
  * button is publisher
  * some busines logic method is subscriber

## Advantages
* open closed principle
  * can easily add new subsribers and remove them dynamically
* run time changes
## Disadavantages
* subscribers are notified randomly
  
## Relations
* sender and receiver perspective

* mediator
  * very similiar but both have different intents
  * observer allows for dynamic one way communication
  * mediator is used in order to avoid tight coupling  
  * mediator can be implemented using observer
    * all compononents are publishers and mediator is a subscriber
    * or mediator is a publisher and compoenents subscribe or unsubscribe from his events (or dont care)


# State
* used when object remembers some internal state
* used when many if statements or huge switchs are involved
* from the outside, it looks like its completelely chaning its implementation

## Example
### Drawing program
* tool button, we want to change its behaviour based on user preference
* soometimes it is a pen, rubber, select tool, ...

```cs
public void Action() {
  if(tool == "Brush") {

  }
  else if (tool == "pen") {

  }
  ...
}

// or

public void Action() {
  switch(tool) {
    ...
  }
}
```
* it behaves like finite state machine
* we want to change behaviour at run time

* this solution is really bad

## Good solution
* use state pattern

## Actors
1. Context
* what keeps track of current state

2. IState
* some action()

3. State
* implements IState

* Context delegates work on current state

```cs
public class Cursor : IContext {
  private Tool tool;

  public Cursor() {
    // brush is default/starting state
    tool = new Brush(this);
  }

  public void Action() {
    tool.Action();
  }

  // can be attached to button on GUI
  public void OnPenSelected() {
    tool.ChangeToPen(); 
  }

  public void SetState(Tool state) {
    tool = state;
  }
}

// tool is state
public class Brush : Tool {
  private IContext cursor;

  public Tool(IContext cursor) {
    this.cursor = cursor;
  }

  public void Action() {
    // on brush action
  }

  public void ChangeToPen() {

    // creates new instance all the time, room for improvement
    cursor.SetState(new Pen());
  }

  public void ChangeToBrush() {
    return;
  }
}
```
* states can be either changed by states (above) or context
* state managment either new instances (above)
* or better, states live in context (tool has reference on context)

## Advantages
* single responsibility
  * each state does his own work
* open/closed principle
  * can easily remove/add new states

## Disadvantages
* overkill if little number of states or the job of each state isnt that significant
* too many states - too many transition methods
  * can be saved by baseState iterlayer between IState and concrete states

## Relations
* strategy
  * both change the behaviour of object at runtime
  * strategies are independent of each other, but states know about each other (eg they can change from their state to another)

* bridge, strategy, adapter
  * they all delegate work to someone else
  * but different intents

# Strategy
* abstract algorithm seperated from concrete implementation

## Example
* app that finds shortest route between two points by car
* later we want to add to find shortest route by boat
* walk, trams, buses and trains, etc ...
* one concrete algorithm - find route, concrete implementations - by specific vehicle

## Actors
1. Context
2. IStrategy
3. Concrete strategy

```cs
public interface IStrategy {
  // no need for void
  public void Action();
}

public class Context {
  private IStrategy strategy;

  public Context(IStrategy strategy) {
    this.SetStrategy(strategy);
  }

  // no need for void
  public void Action() {
    strategy.Action();
  }

  public void SetStrategy(IStrategy strategy) {
    this.strategy = strategy;
  }
}

public class Strategy1 : IStrategy {
  public void Action() {
    // concrete action
  }
}

public class Strategy2 : IStrategy {
  public void Action() {
    // concrete action
  }
}


// client
IStrategy strategy = new Strategy1();
Context context = new Context(strategy);

context.Action();


// can change strategies at run time
context.SetStrategy(new Strategy2());
context.Action();
```

## When to use
* different variants of an algorithm at run time
* objects that differ only in some specific actions
  * can keep it as one and make actions the hieararchy
* use when massive conditional operators used to switch between concrete algorithms

## Advantages
* run time swapping of algos
* seperates abstraction from implementation
* uses composition - flexible
* open/closed principle
  * can easily change strategies

## Disadvantages
* delegates are better
  * not polluting and bloating your code with extra classes and interfaces
* overkill if only handful of algos
* clients must be aware of strategies
  * can be overcome by parameterless constructor that sets strategy to some default one (but possible it doesnt have access)

## Relations
* command

* decorator

* template method
  * strategy allows for run time changes, its based on composition
  * template method is static, its based on inheritance

# Visitor
* does external work on some collection of objects

## Problem
* Export graph to XML
* graph's nodes are different objects (following the same interface )

## Bad solution
* add ExportToXML() to each node
* traverse the graph
### Why it's bad
* changes to all subclasses of node (need for re-testing)
* violates single responsibility principle
  * concrete node should care only about eg geodata or smthg like this, not about XML serializing
* violates open/closed principle
  * bloats the code, if other serialization is added

## Good solution
* Use visitor pattern

## Actors
1. Element
2. Concrete element
3. Visitor
4. Concrete visitor
5. IVisitable
  * optional, can be already in element interface

```cs
// IVisit
public interface IVisitable {
  public void Visit(Visitor visitor);
}

// Element
public interface INode : IVisitable {
  //some actions
}

// concrete element
public class City : INode {
  ...

  // double dispatch
  public void Visit(Visitor visitor) {
    visitor.OnCity();
  }

}

// concrete element
public class Country : INode {
  ...

  public void Visit(Visitor visitor) {
    visitor.OnCountry();
  }
}
...

// visitor handler - visitor
public interface IVisitor {}

// concrete visitor
public class XmlParser : IVisitor {
  private File file;
  public XmlParser(string fileToWriteTo) {
    file = new Stream(fileToWriteTo);
  }


  public void onCity() {
    // parses city to file
  }

  public void onCountry() {
    // parses country to file
  }
  ...
}

// client

// get nodes from the map
IEnumerable<INode> nodes = map.GetNodes();
Visitor XmlParser = new XmlParser();

foreach(var node in nodes) {
  node.Visit(XmlParser);
}
```

## When to use
* when you have collection of complex objects and you want to execute some concrete function on each element
* objects can focus on their bussines logic

## Advantages
* open / closed principle
  * can easily add new visitors
* single responsibility principle
  * separates external logic from busines logic of object

## Disadvantages
* each time change to some element is made, each visitor must be changed accordingly
* visitor might lack access to private fields

## Relations
* composite
  * apply visitor on entire composite tree

* command
  * visitor is powerful version of command
  * command seperates method/action from concrete object who doest the action
  * visitor does the same thing
    * can execute operation over various classes
  
* iterator
  * traverse some complex collection and apply visitor action to each element

# Template method
* abstracts algorithm to steps, that can be either identical to each implementation or implemented by its subclasses

## Example
* we want to simulate behavior of entities in a game
  * monsters, humans, animals, ...

```cs
public class GameAI {
  private List<Resource> resources;
  ...

  public void Step() {
    CollectResources();
    BuildStuctures();
    BuildUnits();
    Attack();
  }

  // some can be already implemented
  public virtual void CollectResources() {
    foreach(var resource in Surrondings(this).GetResources()) {
      resources.Add(resource);
    }
  }

  // some needs to be overriden
  public virtual void BuildStructures();
  public virtual void BuildUnits();
  
  // another template method 
  public void Attack() {
    Enemy enemy = Surrondings.GetEnemies().GetNeariest();

    if(enemy == null)
      sendScouts(10);
    else
      sendWarriors(10);
  }

  private virtual void sendScouts(int n) {
      Surrondings.Spawn(EntitiesFactory.GetScouts(n));
  }

  private virtual void sendWarriors(int n) {
      Surrondings.Spawn(EntitiesFactory.GetWarriors());
  }
}

public class HumanAI : GameAI{
  public override void BuildResources() {
    // concrete human implementation
  }
  ...
}

// client code
var humans = Surrondings.GetHumans(Location.NearbyOnly);
var monsters = Surrondins.GetMonsters(Location.NearbyOnly);

foreach(var human in humans) {
  human.Step();
}

foreach(var monster in monsters) {
  monster.Step();
}
```

## When to use
* keep the structure of some algorithm the same but concrete steps might differ and some not
* extract algorithm from different classes that differ only in this algorithm
  * -> one object (or at least significantly less)
  * seperation of concerns
  * algorithm change doesnt require change of each object

## Advantages
* seperation of concerns
  * moduling, change of subalgorithm doesnt change other algorithms or the whole algorithm
  * easy to maintain
* reduces duplicate codes

## Disadvantages
* predefined skeleton of algorithm might be limiting
* the more steps they have the harder to maintain

## Relations
* almost everything can be a step in template method
  * calling command
  * calling strategy
  * chaing of responsibility 
  ...

* strategy
  * they both change behavior dynamicaly
  * template method is based on inheritance, its static
  * strategy is dynamic