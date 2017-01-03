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
