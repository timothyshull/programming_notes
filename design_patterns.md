# Creational Patterns
- two main methods for parameterizing a system:
    - subclass the classes that create objects
        - Factory Method and offshoots
        - requires continued subclassing which can cascade changes and becomes brittle
    - use object composition by defining an object that is responsible for creating a class
        - Abstract Factory, Builder, Prototype
- considerations:
    - proliferation of subclasses (worst: Factory Method -> Abstract Factory -> Builder -> Prototype)
    - Factory Method provides more customizability
    - Abstract Factory, Builder and Prototype are more flexible but more complicated
      than Factory Method

## Abstract Factory
- intent:
    - provide an interface for creating families of related or dependent objects
      without specifying their concrete classes

- use when:
    - a system should be independent of how its products are created, composed and represented
    - a system should be configured with one of multiple families of products
    - a family of related product objects is designed to be used together and this constraint needs to be enforced
    - you want to provide a class library of products and you want to reveal just their interfaces not their implementations

 - participants:
    - Abstract Factory
    - Concrete Factory
    - Abstract Product
    - Concrete Product
    - Client

 - collaborations:
    - single Concrete Factory at runtime for classes of products
    - Abstract Factory defers object creation to Concrete Factory

 - consequences:
    - isolates concrete classes
    - makes exchanging product families easy
    - promotes consistency among products
    - supporting new kinds of products is difficult

 - implementation:

 - related:
    - implemented using factory methods or prototypes
    - concrete factories can be singletons

## Builder
- intent:
    - separate the construction of a complex object from its representation so that
      the same construction process can create different representations

- use when:
    - the algorithm for creating a complex object should be independent of the parts that make
      up the object and how they are assembled
    - the construction process must allow different representations for the object that's
      constructed

 - participants:
    - Concrete Builder
    - Director (constructs object using the Builder interface)
    - Product

 - collaborations:
    - client creates a Director and configures it with a Builder
    - Director notifies the Builder when a product component should be built
    - Builder composes product on request from Director
    - client retrieves product from the Builder

 - consequences:
    - can vary a product's internal representation
    - produces code for construction and representation
    - allows more fine grained control over the construction process

 - implementation:

 - related:
    - Abstract Factory vs Builder:
        - Builder - step by step construction of an object at a time, returns to
          client on completion
        - Abstract Factory - constructs families of products and returns immediately
    - Builder often builds Composite

## Factory Method
- intent:
    - define an interface for building an object but let subclasses decide which class to instantiate
    - allows a class to defer instantiation to subclasses

- use when:
    - a class can't anticipate the class of objects it must create
    - a class wants its subclasses to specify the objects it creates
    - classes delegate responsibility to one of several helper subclasses, and you want
      to localize the knowledge of which helper subclass is the delegate

 - participants:
    - Product
    - Concrete Product
    - Creator (Application)
    - Concrete Creator (Application Instance)

- collaborations:
    - creator relies on subclasses to define the factory method so that it returns
      an instance of the appropriate Concrete Product

- consequences:
    - provides hooks for subclasses
    - connects parallel class hierarchies

- implementation:

- related:
    - Abstract Factory is often implemented using a Factory Method
    - usually called within Template Methods
    - Prototypes don't require subclassing Creator but often require an initialize
      operation (for Products). Creator uses Initialize to initialize the object but
      Factory Method does not require this.

## Prototype
- intent:
    - specify the kinds of objects to create using a prototypical instance
    - create new objects by copying the prototype

- use when:
    - classes to instantiate are specified at run time (e.g. dynamic loading)
    - avoid building a class hierarchy of Factories to parallel a Product class hierarchy
    - when instances of a class can have one of a few combinations of state (create
      a handful of prototypes and clone them when necessary)

- participants:
    - Prototype
    - Concrete Prototype
    - Client

- consequences:
    - ability to add and remove classes at run time
    - specify new objects by varying values
    - specify new objects to copy structure
    - reduce subclassing
    - dynamically configure an application with classes

- implementation:

- related:
    - Prototype and Abstract Factory can be used in place of one another but
      an Abstract Factory can use Prototypes to create instances of Products
    - Composite and Decorator usage benefits from Prototypes

## Singleton
- intent:
    - ensure only one instance of a class and provide global access point

- use when:
    - there must be one instance of a class with a well-known access point
    - when the single instance should be extensible by subclassing and clients
      should be able to use an extended instance without modifying their code

- participants:
    - Singleton

- collaborations:

- consequences:
    - controlled access to sole instance
    - reduced name space
    - permits refinements of operations and representation
    - permits a variable number of instances
    - more flexible than class operations

- implementation:

- related:
    - many patterns can be implemented using a Singleton:
        - Abstract Factory
        - Builder
        - Prototype

# Structural Patterns
- contain many similarities across patterns, differences can sometimes be subtle
- Adapter vs. Bridge:
    - Adapter - resolves incompatibilities, generally after-the-fact
    - Bridge - provides stable interface to possibly differing implementations, generally
      as an initial design decision

- Facade is not an Adapter as it defines a new interface to unify multiple interfaces
- Composite vs. Decorator vs. Proxy:
    - Composite and Decorator - Decorator is not just a degenerate Composite
    - Decorator - adds responsibilities to objects without subclassing
    - Composite - provides structure to multiple objects
    - often used together
    - Proxy - like Decorator, composes an object to provide an identical interface
      to clients (i.e. does not change original interface)
    - not used for recursive composition
    - provides access control primarily, hiding various details like location and
      time of initialization
    - subject provides/defines functionality
    - Decorator remedies the situation where an object's total functionality cannot
      be determined at compile time (conveniently, i.e. without excessive subclassing)

## Adapter
- intent:
    - convert the interface of a class into another interface
    - allows interoperability between classes that would be incompatible

- use when:
    - you want to use an existing class with an interface that does not match your needs
    - create a reusable class that cooperates with unrelated or unknown classes
    - can adapt the parent class for the interface of several subclasses

- participants:
    - Target
    - Client
    - Adaptee
    - Adapter

- collaborations:
    - Clients call operations on the Adapter which in turn calls operations on the Adaptee

- consequences:
    - Class vs Object Adapter (subclassing vs pointer referencing):
        - Class:
            - commits to a single concrete class
            - overrides some of Adaptee's behaviors (as a subclass of Adaptee)
            - introduces only one object with no additional pointer reference to Adaptee
        - Object:
            - single Adapter works with many Adaptees (Adaptee and subclasses)
            - can add functionality to Adaptee
            - more difficult to override behavior
    - exist on a spectrum of work done (and overridden/accessible behavior)
    - pluggable adapters (can subclass adapters and modify behavior as needed)
    - two-way adapters for transparency (multiple inheritance to remedy the potential lack of
      interface conformance caused by Adapters)

- implementation:

- related:
    - Bridge - similar but meant to allow two interfaces to vary independently (Adapter
      changes an interface)
    - Decorator - adds to an object but does not change the interface, also supports recursive composition
    - Proxy - provides a surrogate or another object but does not change interface

## Bridge
- intent:
    - decouple an abstraction from its implementation so that the two can vary independently

- use when:
    - avoid permanent binding between an abstraction and its implementation
    - both abstraction and implementation should be extensible through subclassing
    - changes in implementation should not affect clients
    - hide implementation completely from clients (PIMPL)
    - proliferation of subclasses necessitates splitting an object into two parts
    - share implementation between multiple objects but hide this from clients

- participants:
    - Abstraction
    - Refined Abstraction
    - Implementor
    - Concrete Implementor

- collaborations:
    - Abstraction forwards requests to the Implementor object

- consequences:
    - decoupling interface and implementation
    - improved extensibility
    - hiding implementation details from clients

- implementation:

- related:
    - Abstract Factory can create and configure a Bridge
    - Adapters are generally created and applied after the fact whereas Bridges
      are created up front to allow abstractions and implementations to vary
      independently

## Composite
- intent:
    - compose objects into tree structures to represent part-whole hierarchies
    - allows clients to treat individual objects and compositions of objects
      uniformly

- use when:
    - need to represent part-whole hierarchies
    - need to allow clients to ignore the difference between compositions of objects
      and individual objects

- participants:
    - Component
    - Leaf
    - Composite
    - Client

- collaborations:
    - Clients go through the Component to access a Composite, which either forwards
      directly to a Leaf or passes through a hierarchy to a Leaf

- consequences:
    - defines hierarchies consisting of primitive objects and composite objects
    - makes the client simple
    - makes it easier to add new kinds of components
    - can make the design too general

- implementation:

- related patterns:
    - component-parent link can be used for Chain of Responsiblity
    - Decorator and Composite are often used together, with Decorator supporting
      the Component interface
    - Flyweight allows sharing of components but they can no longer refer to parents
    - Iterator can be used to traverse composites
    - Visitor localizes operations and behavior that would otherwise be distributed
      across Composite and Leaf classes

## Decorator
- intent:
    - attach responsibility to an object dynamically
    - flexible alternative to subclassing for extended functionality

- use when:
    - add responsibility to an object dynamically without affecting other objects
    - responsibilities of an object/class can be withdrawn
    - extension by subclassing is impractical (large number of independent extensions
      are possible or a class is hidden or not capable of being subclassed)

- participants:
    - Component
    - Concrete Component
    - Decorator
    - Concrete Decorator

- collaborations:
    - forwards requests to Component

- consequences:
    - more flexible than static inheritance
    - avoids feature laden classes high in a hierarchy
    - a decorator and its component are not identical
    - lots of little objects

- implementation:

- related:
    - Adapter - changes an interface whereas a Decorator changes an object's responsibilities
    - Composite - a Decorator is a Composite with only one Component
    - Strategy - Decorator changes the skin, Strategy changes the guts

## Facade
- intent:
    - provide a unified interface to a set of interfaces in a subsystem
    - provides a higher level interface that makes a system easier to use

- use when:
    - want to provide a simple interface to a complex system/subsytem
    - want to decouple a client from a dependency-laden abstraction/implementation class
      network
    - want to provide a (simplified/single) entry point to a layered subsystem

- participants:
    - Facade
    - subsytem classes

- collaborations:
    - forwards requests from clients to various subsystem objects
    - clients do not need to access subsystem directly

- consequences:
    - hides subsystem from clients, simplifying client code/interactions
    - promotes weak coupling/decoupling
    - does not prevent an application from using subsystem classes if necessary

- implementation:

- related:
    - Facade can provide an interface to an Abstract Factory or an Abstract Factory
      can be used in place of a Facade
    - Mediator is similar but is used to coordinate various Colleagues without reducing
      awareness between Colleagues
    - Facades are often Singletons

## Flyweight
- intent:
    - use sharing to support large numbers of objects efficiently

- use when:
    - an application uses a large number of objects
    - storage costs are high because of the sheer quantity of objects
    - most objects can use extrinsic state
    - many groups of objects may be replaced by relatively few shared objects after
      removing extrinsic state
    - application does not depend on object identity

- participants:
    - Flyweight
    - Concrete Flyweight
    - Unshared Concrete Flyweight (row, column)
    - Flyweight Factory
    - Client

- collaborations:
    - Flyweight state is characterized as extrinsic or intrinsic
        - intrinsic state is stored in the Concrete Flyweight
        - extrinsic state is calculated by clients and passed to Flyweight instances
          when their operations are invoked
    - clients should obtain Flyweights exclusively through a Flyweight Factory (which manages
      sharing)

- consequences:
    - may incur run time cost of generating extrinsic state
    - promotes storage savings by:
        - reduction in total number of instances
        - amount of intrinsic state per object
        - computation of extrinsic state vs. storage of extrinsic state
    - Flyweights are often combined with Composite to create a hierarchy

- implementation:

- related:
    - combined with Composite to create hierarchy (DAG with shared leaf nodes)
    - best to implement State and Strategy as Flyweights

## Proxy
- intent:
    - provide a surrogate or placeholder for another object to control access to it

- use when:
    - remote proxy:
        - representative for an object in a different address space (i.e. machine, network)
    - virtual proxy:
        - creates expensive objects on demand
    - protection proxy:
        - controls access to an object (and manages access rights)
    - smart reference:
        - replaces bare pointer
        - reference counting
        - lazy loading, caching, memory management, etc
        - locking/access control

- participants:
    - Proxy
    - Subject
    - Real Subject

- consequences:
    - can hide location (address space) of object
    - can optimize with lazy creation/caching
    - allows additional housekeeping for object access
    - can use for copy-on-write

- implementation:

- related:
    - Adapter - provides a different interface whereas Proxy provides the same interface
      (NOTE: access control can result in a subset of the interface)
    - Decorator - similar but adds responsibilities whereas Proxies control access
      both vary in degree to their implementation

# Behavioral Patterns

