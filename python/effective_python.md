# 1. Pythonic Thinking
## 3. Know the difference between bytes, str, and unicode
- Python 3:
    - bytes - 8-bit values
    - str - unicode characters
    - cannot be used together with operators, etc without conversion
- Python 2:
    - str - 8 - bit
    - unicode - unicode characters
    - can interoperate if str only contains 7-bit ASCII
- use helper functions
- use 'rb' and 'wb' for file access in binary mode

## 4. Write Helper Functions Instead of Complex Expressions
- Python’s syntax makes it all too easy to write single-line expressions that are overly complicated and difficult to read
- Move complex expressions into helper functions, especially if you need to use the same logic repeatedly
- The if/ else expression provides a more readable alternative to using Boolean operators like or and and
  in expressions

## 5. Know How to Slice Sequences
- simplest with list, str, bytes
- can extend any type using __getitem__ and __setitem__
- basic form `list[start:end]` (inclusive, exclusive)
- when slicing from zero, leave out 0 (e.g. `a[:5] == a[0:5]`)
- when slicing to the end of a list, leave out the end index (e.g. `a[5:] == a[5:len(a)]`)
- using negative numbers gives an offset relative to the end of a list
```
a[:] # [' a', 'b', 'c', 'd', 'e', 'f', 'g', 'h']
a[: 5] # [' a', 'b', 'c', 'd', 'e'] 
a[:-1] # [' a', 'b', 'c', 'd', 'e', 'f', 'g'] 
a[ 4:] # [' e', 'f', 'g', 'h'] 
a[-3:] # [' f', 'g', 'h'] 
a[ 2: 5] # [' c', 'd', 'e'] 
a[ 2:-1] # [' c', 'd', 'e', 'f', 'g'] 
a[-3:-1] # [' f', 'g']
```
- slicing handles out of bounds start and end indexes whereas element access does not

- Avoid being verbose: Don’t supply 0 for the start index or the length of the sequence for the end index
- Slicing is forgiving of start or end indexes that are out of bounds, making it easy to express slices on the front or
  back boundaries of a sequence (like `a[:20]` or `a[-20:]`)
- Assigning to a list slice will replace that range in the original sequence with what’s referenced even if their
  lengths are different

## 6: Avoid Using start, end, and stride in a Single Slice
- stride of slice `list[start:end:stride]` (i.e. every nth item)
- negative stride works for byte strings and ASCII, but it will break for Unicode encoded
  as UTF-8
- Specifying start, end, and stride in a slice can be extremely confusing
- Prefer using positive stride values in slices without start or end indexes
- Avoid negative stride values if possible
- Avoid using start, end, and stride together in a single slice
- If you need all three parameters, consider doing two assignments (one to slice, another to stride) or using
  islice from the itertools built-in module

## 7. Use List Comprehensions Instead of map and filter
- list comprehension - syntax for deriving a list from an iterable
- clearer than map builtin with a lambda
- can use filter with map to achieve same simpler syntax as list comprehensions
- List comprehensions are clearer than the map and filter built-in functions because they don’t require extra lambda
  expressions
- List comprehensions allow you to easily skip items from the input list, a behavior map doesn’t support without help
  from filter
- Dictionaries and sets also support comprehension expressions.

## 8. Avoid More Than Two Expressions in List Comprehensions
- support multiple levels of looping
```
matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
flat = [x for row n matrix for x in row]

# basic looping
flat = []
for sublist1 in three_dim:
    for sublist2 in sublist1:
        flat.extend(sublist2)
```
- also support multiple ifs
```
matrix = [[ 1, 2, 3], [4, 5, 6], [7, 8, 9]]
filtered = [[ x for x in row if x % 3 = = 0] for row in matrix if sum( row) > = 10]
```
- List comprehensions support multiple levels of loops and multiple conditions per loop level
- List comprehensions with more than two expressions are very difficult to read and should be
  avoided

## 9. Consider Generator Expressions for Large Comprehensions
- list comprehensions may create a whole new list for each item in the input sequence which
  is not fine for large inputs (takes memory)
- generator expressions - generalization of list comprehensions and generators that don't
  materialize the whole output sequence when run, instead generate one element at a time
- syntax is like list comprehensions but between `()`
```
it = (len(x) for x in open('/tmp.txt'))
```
- generator expressions can be composed together
- List comprehensions can cause problems for large inputs by using too much memory
- Generator expressions avoid memory issues by producing outputs one at a time as an iterator
- Generator expressions can be composed by passing the iterator from one generator expression into the for
  subexpression of another
- Generator expressions execute very quickly when chained together

## 10. Prefer enumerate Over range
- enumerate provides concise syntax for looping over an iterator and getting the index of each item from the iterator
  as you go
- Prefer enumerate instead of looping over a range and indexing into a sequence
- You can supply a second parameter to enumerate to specify the number from which to begin counting (zero is the default)

## 11. Use zip to Process Iterators in Parallel
- in Python 3, zip wraps two or more iterators with a lazy generator which yields tuples that
  contain the next value from each iterator
- The zip built-in function can be used to iterate over multiple iterators in parallel
- In Python 3, zip is a lazy generator that produces tuples
- In Python 2, zip returns the full result as a list of tuples
- zip truncates its output silently if you supply it with iterators of different lengths
- The zip_longest function from the itertools built-in module lets you iterate over multiple
  iterators in parallel regardless of their lengths

## 12. Avoid else Blocks After for and while Loops
- can put an else block after a loop's repeated interior block
```
for i in range(3):
    print('Loop %d' % i)
else:
    print('Else block!')
```
- else block here does not happen with a call to break
- executes immediately when for statement is passed an empty list
- also runs with while loops when the while loop is initially False
- Python has special syntax that allows else blocks to immediately follow for and while loop interior blocks
- The else block after a loop only runs if the loop body did not encounter a break statement
- Avoid using else blocks after loops because their behavior isn’t intuitive and can be confusing

## 13. Take Advantage of Each Block in try/except/else/finally
- each block serves a different unique function
- finally - for reliably running clean-up code before an exception propagates
- else - else in try/except/else blocks run when try block does not raise an exception
    - minimizes amount of code in try block
    - improves readability
- The try/ finally compound statement lets you run cleanup code regardless of whether exceptions were raised in the try
  block
- The else block helps you minimize the amount of code in try blocks and visually distinguish the success case from the
  try/except blocks
- An else block can be used to perform additional actions after a successful try block but before common cleanup in a
  finally block


# 2. Functions
## 14. Prefer Exceptions to Returning None
- None commonly used to avoid throwing exceptions but is ultimately unclear across APIs
- Functions that return None to indicate special meaning are error prone because None and other values (e.g., zero, the
  empty string) all evaluate to False in conditional expressions
- Raise exceptions to indicate special situations instead of returning None
- Expect the calling code to handle exceptions properly when they’re documented

## 15. Know How Closures Interact with Variable Scope
- Python supports closures
    - closures - functions that refer to variables from the scope in which they were defined
- Functions are first-class objects in Python, meaning you can refer to them directly, assign them to variables, pass
  them as arguments to other functions, compare them in expressions and if statements, etc.
- Python has specific rules for comparing tuples
    - first compares items in index zero, then index one, then index two, and so on
- Python interpreter traverses the scope to resolve a variable reference in the following order
    1. current function’s scope
    2. Any enclosing scopes (like other containing functions)
    3. scope of the module that contains the code (also called the global scope)
    4. built-in scope (that contains functions like len and str)
- `nonlocal` keyword can be used for getting data from outside of a closure in Python 3
```
def sort_priority3(numbers, group):
    found = False
    def helper(x):
        nonlocal found
        if x in group:
            found = True
            return (0, x)
        return (1, x)
    numbers.sort(key = helper)
    return found
```
- Python 2 does not support nonlocal so need to use a work-around
- Closure functions can refer to variables from any of the scopes in which they were defined
- By default, closures can’t affect enclosing scopes by assigning variables
- In Python 3, use the nonlocal statement to indicate when a closure can modify a variable in its enclosing scopes
- In Python 2, use a mutable value (like a single-item list) to work around the lack of the nonlocal statement
- Avoid using nonlocal statements for anything beyond simple functions

## 16. Consider Generators Instead of Returning Lists
- Using generators can be clearer than the alternative of returning lists of accumulated results
- The iterator returned by a generator produces the set of values passed to yield expressions within the generator
  function’s body
- Generators can produce a sequence of outputs for arbitrarily large inputs because their working memory doesn’t
 include all inputs and outputs

## 17. Be Defensive When Iterating Over Arguments
- convert potentially passed generators using list() ctor
- better to use iterable objects than lambdas (define __iter__ as a generator)
- Beware of functions that iterate over input arguments multiple times
- If these arguments are iterators, you may see strange behavior and missing values
- Python’s iterator protocol defines how containers and iterators interact with the iter and next built-in functions,
  for loops, and related expressions
- can easily define iterable container type by implementing the __iter__ method as a generator
- can detect that a value is an iterator (instead of a container) if calling iter on it twice produces the same result,
  which can then be progressed with the next built-in function

## 18. Reduce Visual Noise with Variable Positional Arguments
- optional positional arguments (often called star args (*args)) can remove visual noise
```
def log(message, *values):
    if not values:
        print(message)
     else:
        values_str = ', '.join(str(x) for x in values)
        print('%s: %s' % (message, values_str))
```
- call with *arg when inputting at call site
- the variable arguments are always turned into a tuple before they are passed to your function
    - if the caller of the function uses the * operator on a generator, it will be iterated until
      it’s exhausted
    - resulting tuple will include every value from the generator, which could consume a
      lot of memory and cause program to crash
- Functions can accept a variable number of positional arguments by using *args in the def statement
- can use the items from a sequence as the positional arguments for a function with the * operator
- Using the * operator with a generator may cause your program to run out of memory and crash
- Adding new positional parameters to functions that accept *args can introduce hard-to-find bugs

## 19. Provide Optional Behavior with Keyword Arguments
- all positional arguments can also be passed by keyword
- best practice is to always specify optional arguments using the keyword names and never pass them as positional
  arguments
- NOTE: backwards compatibility using optional keyword arguments is important for functions that
  accept *args
    - an even better practice is to use keyword-only arguments
- Function arguments can be specified by position or by keyword
- Keywords make it clear what the purpose of each argument is when it would be confusing with only positional arguments
- Keyword arguments with default values make it easy to add new behaviors to a function, especially when the function
  has existing callers
- Optional keyword arguments should always be passed by keyword instead of by position

## 20. Use None and Docstrings to Specify Dynamic Default Arguments
- Default arguments are only evaluated once: during function definition at module load time
- This can cause odd behaviors for dynamic values (like {} or [])
- Use None as the default value for keyword arguments that have a dynamic value
- Document the actual default behavior in the function’s docstring

## 21. Enforce Clarity with Keyword-Only Arguments
- in Python 3, you can demand clarity by defining your functions with keyword-only arguments
    - these arguments can only be supplied by keyword, never by position
- the * symbol in the argument list indicates the end of positional arguments and the beginning of keyword-only arguments
```
def safe_division_c( number, divisor, *, ignore_overflow = False, ignore_zero_division = False):
```
- need to use **kwargs in Python 2
- Keyword arguments make the intention of a function call more clear
- Use keyword-only arguments to force callers to supply keyword arguments for potentially confusing
  functions, especially those that accept multiple Boolean flags
- Python 3 supports explicit syntax for keyword-only arguments in functions
- Python 2 can emulate keyword-only arguments for functions by using ** kwargs and manually raising TypeError exceptions


# 3. Classes and Inheritance
## 22. Prefer Helper Classes Over Bookkeeping with Dictionaries and Tuples
- avoid dictionaries that contain dictionaries
    - as soon as bookkeeping gets complicated, break a structure into helper classes
- collections.namedtuple is useful but extending it indefinitely makes becomes an anti-pattern
- limitations of namedtuple
    - can’t specify default argument values for namedtuple classes
    - become unwieldy when data may have many optional properties
    - if using more than a handful of attributes, defining your own class may
      be a better choice
    - attribute values of namedtuple instances are still accessible using numerical
      indexes and iteration
    - especially in externalized APIs, this can lead to unintentional usage that makes it harder to move to a real class
      later
    - if not in control of all of the usage of namedtuple instances, better to define your own class
- Avoid making dictionaries with values that are other dictionaries or long tuples
- Use namedtuple for lightweight, immutable data containers before you need the flexibility of a full class
- Move your bookkeeping code to use multiple helper classes when your internal state dictionaries get complicated

## 23. Accept Functions for Simple Interfaces Instead of Classes
- instead of defining and instantiating classes, functions are often all you need for simple interfaces between
  components in Python
- references to functions and methods in Python are first class, meaning they can be used in expressions like any
  other type
- the __call__ special method enables instances of a class to be called like plain Python functions
- when you need a function to maintain state, consider defining a class that provides the __call__ method instead of
  defining a stateful closure

## 24. Use @classmethod Polymorphism to Construct Objects Generically
- polymorphism is a way for multiple classes in a hierarchy to implement their own unique versions of a method
- allows many classes to fulfill the same interface or abstract base class while providing different functionality
- Python only supports a single constructor per class, the __init__ method
- Use @classmethod to define alternative constructors for your classes
- Use class method polymorphism to provide generic ways to build and connect concrete subclasses

- TODO: show examples here

## 25. Initialize Parent Classes with super
- if your class is affected by multiple inheritance (something to avoid in general
  calling the superclasses’ __init__ methods directly can lead to unpredictable behavior
- one problem is that the __init__ call order isn’t specified across all subclasses
- another problem occurs with diamond inheritance
    - Diamond inheritance happens when a subclass inherits from two separate classes that have the same superclass
      somewhere in the hierarchy
    - Diamond inheritance causes the common superclass’s __init__ method to run multiple times, causing unexpected behavior
- the super built-in function works well, but it still has two noticeable problems in Python 2:
    - Its syntax is a bit verbose - have to specify the class you’re in, the self object, the method name
      (usually __init__), and all the arguments
    - have to specify the current class by name in the call to super
    - if the class’s name is changed also need to update every call to super
- Python 3 fixes these issues by making calls to super with no arguments equivalent to calling
  super with __class__ and self specified
- In Python 3, always use super because it’s clear, concise, and always does the right thing
- Python’s standard method resolution order (MRO) solves the problems of superclass initialization order and diamond
  inheritance
- Always use the super built-in function to initialize parent classes

## 26. Use Multiple Inheritance Only for Mix-in Utility Classes
- Avoid using multiple inheritance if mix-in classes can achieve the same outcome
- Use pluggable behaviors at the instance level to provide per-class customization when mix-in classes may require it
- Compose mix-ins to create complex functionality from simple behaviors

## 27. Prefer Public Attributes Over Private Ones
- Private attributes aren’t rigorously enforced by the Python compiler
- Plan from the beginning to allow subclasses to do more with your internal APIs and attributes instead of locking them
  out by default
- Use documentation of protected fields to guide subclasses instead of trying to force access control with private
  attributes
- Only consider using private attributes to avoid naming conflicts with subclasses that are out of your control

## 28. Inherit from collections.abc for Custom Container Types
- reports errors when a method is not overridden that should be implemented
- Inherit directly from Python’s container types (like list or dict) for simple use cases
- Beware of the large number of methods required to implement custom container types correctly
- Have your custom container types inherit from the interfaces defined in collections.abc to ensure that your classes
  match required interfaces and behaviors


# 4. Metaclasses and Attributes
- allow interception of class statement and provide special behavior each time a class is
  defined
- supplies built-in features for dynamically customizing attribute access
- dynamic attributes enable you to override objects and cause unexpected side effects
- metaclasses can create extremely bizarre behaviors that are unapproachable to newcomers
- important to follow the rule of least surprise and only use these mechanisms to implement
  well-understood idioms

## 29. Use Plain Attributes Instead of Get and Set Methods
- Define new class interfaces using simple public attributes, and avoid set and get methods
- Use @property to define special behavior when attributes are accessed on your objects,
  if necessary
- Follow the rule of least surprise and avoid weird side effects in your @property methods
- Ensure that @property methods are fast; do slow or complex work using normal methods

## 30. Consider @property Instead of Refactoring Attributes
- Use @property to give existing instance attributes new functionality
- Make incremental progress toward better data models by using @property
- Consider refactoring a class and all call sites when you find yourself using @property too heavily

## 31. Use Descriptors for Reusable @property Methods
