# Ch. 1 - The Python Data Model

## How Special Methods Are Used
- meant to be called by Python interpreter and not programmer
- can be used to emulate numeric types with operator overloading
- can be used to set how a custom type is printed (__repr__)
    - __str__ is called by the str() constructor and implicitly used by the print function
    - __str__ should return a string suitable for display to end users
    - choose __repr__ if only implementing one because when no custom __str__ is available Python will call __repr__ as
      a fallback
- can be used for arithmetic operators (__add__, __mul__)
- can be used to use custom type as a bool value (__bool__)

## Overview of Special Methods
- string/bytes representation
    - `__repr__`
    - `__str__`
    - `__format__`
    - `__bytes__`
- conversion to number
    - `__abs__`
    - `__bool__`
    - `__complex__`
    - `__int__`
    - `__float__`
    - `__hash__`
    - `__index__`
- collection-like functionality
    - `__len__`
    - `__getitem__`
    - `__setitem__`
    - `__delitem__`
    - `__contains__`
- iteration
    - `__iter__`
    - `__reversed__`
    - `__next__`
- making an object callable
    - `__call__`
- context management
    - `__enter__`
    - `__exit__`
- instance creation and destruction
    - `__new__`
    - `__init__`
    - `__del__`
- attribute management
    - `__getattr__`
    - `__getattribute__`
    - `__setattr__`
    - `__delattr__`
    - `__dir__`
- attribute descriptors
    - `__get__`
    - `__set__`
    - `__delete__`
- class services
    - `__prepare__`
    - `__instancecheck__`
    - `__subclasscheck__`

### Operators
- unary numeric operators
    - `__neg__`
        - `-`
    - `__pos__`
        - `+`
    - `__abs__`
        - `abs()`
- rich comparison operators
    - `__lt__`
        - `<`
    - `__le__`
        - `<=`
    - `__eq__`
        - `==`
    - `__ne__`
        - `!=`
    - `__gt__`
        - `>`
    - `__ge__`
        - `>=`
- arithmetic operators
    - `__add__`
        - `+`
    - `__sub__`
        - `-`
    - `__mul__`
        - `*`
    - `__truediv__`
        - `/`
        - from 2.2 - 2.*, 5 / 2 and 5 // 2 (integer) are equivalent unless adding `from __future__ import division` (with integers)
        - for 3 and with the future import above, returns 2.5
    - `__floordiv__`
        - always returns 2.0 (float) for 5 // 2
        - `//`
    - `__divmod__`
        - `%`
    - `__pow__`
        - `** or pow()`
    - `__round__`
        - `round()`
- reversed arithmetic operators
    - `__radd__`
    - `__rsub__`
    - `__rmul__`
    - `__rtruediv__`
    - `__rfloordiv__`
    - `__rmod__`
    - `__rdivmod__`
    - `__rpow__`
- augmented
    - `__iadd__`
    - `__isub__`
    - `__imul__`
    - `__itruediv__`
    - `__ifloordiv__`
    - `__imod__`
    - `__ipow__`
- bitwise operators
    - `__invert__`
        - `~`
    - `__lshift__`
        - `<<`
    - `__rshift__`
        - `>>`
    - `__and__`
        - `&`
    - `__or__`
        - `|`
    - `__xor__`
        - `^`
- reversed bitwise operators
    - `__rlshift__`
    - `__rrshift__`
    - `__rand__`
    - `__ror__`
    - `__rxor__`
- augmented bitwise operators
    - `__ilshift__`
    - `__irshift__`
    - `__iand__`
    - `__ior__`
    - `__ixor__`

- len is not called as a method because it gets special treatment as part of the Python data model, just like abs
- can also make len work with custom objects using the special method __len__

- special methods allow custom objects to behave like the built-in types
    - enables the expressive coding style the community considers Pythonic
- A basic requirement for a Python object is to provide usable string representations of itself, one used for debugging
  and logging, another for presentation to end users
    - purpose of special methods __repr__ and __str__ exist in the data model
- emulating sequences is one of the most widely used applications of the special methods
- Python offers a rich selection of numeric types, aided by operator overloading, from the built-ins to
  decimal.Decimal and fractions.Fraction, all supporting infix arithmetic operators

- Python data model - used in documentation
    - Python object model - used by many authors
- the Ruby community calls their equivalent of the special methods magic methods.
- the term metaobject protocol is useful to think about the Python data model and similar features in other languages
- metaobject refers to the objects that are the building blocks of the language itself
- in this context, protocol is a synonym of interface
- metaobject protocol ~= object model - an API for core language constructs.
- aspect-oriented programming is much easier to implement in a dynamic language like Python, and several frameworks
  do it, but the most important is zope.interface




# II. Data Structures

# 2. An Array of Sequences
- mastering the standard library sequence types is a prerequisite for writing concise, effective, and idiomatic Python code

## Overview of Built-In Sequences
- mutable
    - list, bytearray, array.array, collections.deque, and memoryview
- immutable
    - tuple, str, and bytes
- flat sequences
    - str, bytes, bytearray, memoryview, and array.array hold items of one type
    - physically store the value of each item within its own memory space, and not as distinct objects
    - more compact, faster, and easier to use
    - limited to storing atomic data such as numbers, characters, and bytes
- container sequences
    - list, tuple, and collections.deque can hold items of different types
    - hold references to the objects they contain, which may be of any type
    - more flexible
    - act surprisingly when they hold mutable objects
    - use with caution with nested data structures

```
Container       Iterable        Sized
__contains__    __iter__        __len__

^               ^               ^
Sequence
__getitem__
__contains__
__iter__
__reversed__
index
count

^
MutableSequence
__setitem__
__delitem__
insert
append
reverse
extend
pop
remove
__iadd__
```

## List Comprehensions and Generator Expressions
- powerful notations to build and initialize sequences, master them
- listcomps and genexps
```
for symbol in symbols:
    codes.append(ord(symbol))

codes = [ord(symbol) for symbol in symbols]
```
- syntax tip - in Python code, line breaks are ignored inside pairs of [], {}, or ()
    - can build multiline lists, listcomps, genexps, dictionaries and the like without using the ugly \ line
      continuation escape
- in Python 2.x, variables assigned in the for clauses in list comprehensions were set in
  the surrounding scope
    - the value of outer variables can be overwritten
- prefer listcomps over builtin map and filter
    - may be faster in some cases but not always
    - test
- to initialize tuples, arrays, and other types of sequences can use listcomp
    - genexp saves memory because it yields items one by one using the iterator protocol instead
      of building a whole list just to feed another constructor
- use the same syntax as listcomps, but are enclosed in parentheses rather than brackets
```
tuple( ord( symbol) for symbol in symbols)
```

## Tuples Are Not Just Immutable Lists
- tuples in Python play two roles
    - records with unnamed fields
    - immutable lists
- when a tuple is used as a record, tuple unpacking is the safest, most readable way of getting at the fields
- the new * syntax makes tuple unpacking even better by making it easier to ignore some fields and to deal with
  optional fields
- can use tuple unpacking with any iterable
    - tuple unpacking -> iterable unpacking
```
a, b, *rest = range( 5)
(0, 1, [2, 3, 4])
```
- can use nested tuple unpacking
```
for name, cc, pop, (latitude, longitude) in metro_areas:
```
- named tuples
    - like tuples, they have very little overhead per instance
    - provide convenient access to the fields by name and a handy ._asdict() to export the record as an OrderedDict.

- tuples as immutable lists
    - tuple supports all list methods that do not support adding or removing items
        - except __reverse__ but reversed(my_tuple) works without it
    - __add__
    - __iadd__
    - append
    - clear
    - __contains__
    - copy
    - count
    - __delitem__
    - extend
    - __getitem__
    - __getnewargs__
    - index
    - insert
    - __iter__
    - __len__
    - __mul__
    - __imul__
    - __rmul__
    - pop
    - remove
    - reverse
    - __reversed__
    - __setitem__
    - sort

## Slicing
- sequence slicing is a favorite Python syntax feature, and it is even more powerful than many realize
- multidimensional slicing and ellipsis (...) notation, as used in NumPy, may also be supported by user-defined sequences
- assigning to slices is a very expressive way of editing mutable sequences
- repeated concatenation as in seq * n is convenient and, with care, can be used to initialize lists of lists containing
  immutable items

## Using + and * with Sequences

## Augmented Assignment with Sequences
- augmented assignment with + = and *= behaves differently for mutable and immutable sequences
    - *= - these operators necessarily build new sequences
    - if the target sequence is mutable, it is usually changed in place — but not always, depending on how the sequence
      is implemented

## list.sort and the sorted Built-In Functions
- the sort method and the sorted built-in function are easy to use and flexible, thanks to the key optional argument
  they accept, with a function to calculate the ordering criterion
- key can also be used with the min and max built-in functions

## Managing Ordered Sequences with bisect
- to keep a sorted sequence in order, always insert items into it using bisect.insort; to search it efficiently, use
  bisect.bisect

## When a List Is Not the Answer
- Python standard library provides array.array.
- NumPy and SciPy are not part of the standard library but should study
- thread-safe collections.deque
    - comparing its API with that of list in Table   2-3 and mentioning other queue implementations in the standard
      library

# 3. Dictionaries and Sets
- Python dicts are highly optimized
- hash tables are the engines behind Python’s high-performance dicts

## Generic Mapping Types
- collections.abc
    - Mapping
    - MutableMapping
    - formalize the interfaces of dict
- often extend collections.UserDict instead
```
Container       Iterable        Sized
__contains__    __iter__        __len__

^               ^               ^
Mapping
__getitem__
__contains__
__eq__
__ne__
get
items
values

^
MutableMapping
__setitem__
__delitem__
clear
pop
popitem
setdefault
update
```
- keys must be hashable
    - an object is hashable if it has a hash value which never changes during its lifetime
      (it needs a __hash__() method), and can be compared to other objects (it needs an __eq__() method)
    - hashable objects which compare equal must have the same hash value
    - the atomic immutable types (str, bytes, numeric types) are all hashable
    - a frozenset is always hashable, because its elements must be hashable by definition
    - a tuple is hashable only if all its items are hashable

## dict Comprehensions
- dictcomp added in Python 2.7
```
country_code = {country: code for code, country in DIAL_CODES}
```

## Overview of Common Mapping Methods
- defaultdict and OrderedDict in collections
    - clear
    - __contains__
    - copy
    - __copy__
    - default_factory
    - __delitem__
    - fromkeys
    - get
    - __getitem__
    - items
    - __iter__
    - keys
    - __len__
    - __missing__
    - move_to_end
    - pop
    - popitem
    - __reversed__
    - setdefault
    - __setitem__
    - update
    - values

### Handling Missing Keys with setdefault
- dict access with `d[k]` raises an error when k is not an existing key
- d.get(k, default) is an alternative to `d[k]` whenever a default value is more convenient
  than handling KeyError
- when updating the value found (if it is mutable), using either __getitem__ or get is
  awkward and inefficient
```
my_dict.setdefault(key, []).append(new_value)

# same as
if key not in my_dict:
    my_dict[key] = []
my_dict[ key]. append( new_value)
```
- setdefault uses a single lookup whereas the second example uses at least two and three
  if its not found

## Mappings with Flexible Key Lookup


# Ch. 2 - An Array of Sequences

## Overview of Built-In Sequences
- container sequences - items of different types (references)
    - list
    - tuple
    - collections.deque
- flat sequences - items of one type (physically store value in memory space)
    - str
    - bytes
    - bytearray
    - memoryview
    - array.array
- mutable
    - list
    - bytearray
    - array.array
    - collections.deque
    - memoryview
- immutable
    - tuple
    - str
    - bytes

- inheritance (see diagram for methods)
```
MutableSequences -> Sequence -> Container
                             -> Iterable
                             -> Sized
```

## List Comprehensions and Generator Expressions
- listcomps and genexps often faster and more readable
- tip - can build multiline lists, listcomps, and genexps within `[], {}, or ()`
- listcomps previously leaked variable names into the surrounding scope (e.g. overrode
  variable values of the same variable name)
    - fixed in Python 3

- map and filter are not necessarily faster
-
```
# arranged by color then size
[(color, size) for color in colors for size in sizes]

# arranged in same order as list from above
for color in colors:
    for size in sizes:
        print((color, size))

# ordered by size as the second element then color
[(color, size) for size in sizes for color in colors]
```
- listcomps only build lists
    - use genexps to fill up sequence types

- genexp
    - can build tuple, array, and other types of sequences
    - enclosed in parens rather than brackets
    - saves memory
        - yields items one by one using the iterator protocol instead of
          building a whole list just to feed another constructor
```
# no paren duplication
tuple(ord( symbol) for symbol in symbols)

# paren duplication due to multi-arg ctor
array.array('I', (ord( symbol) for symbol in symbols))
```
- can use in for loops
```
for tshirt in ('% s %s' % (c, s) for c in colors for s in sizes):
    print( tshirt)
```
- saves the expense of building lists to feed the for loop

## Tuples Are Not Just Immutable Lists
- immutable lists and records with no field names
- as records
    - each item in the tuple holds the data for one field and the position of the
      item gives its meaning
- tuple unpacking can be done
    - to assign elements to variables
    - to assign elements to a format string
    - works with any iterable object
        - only requirement is iterable yields one item per variable in the receiving tuple
        - unless using * to capture excess items
    - tuple unpacking == iterable unpacking
    - can use to unpack as args to function
- can use to swap
```
b, a = a, b
```
- can use as return value from function
- can use _ as a placeholder variable but be careful when writing internationalized
  software (used as an alias to gettext.gettext)
- can use * for excess items, and can appear in any position
```
a, b, *rest = range(5)
```
- works with nested structures
    - could define functions with nested tuples in formal parameters in Python 2
    - cannot in Python 3
- use collections.namedtuple for debugging and more
    - same memory consumed as regular tuple and less than a class
    - two parameters to create a named tuple
        - class name
        - list of field names
            - iterable of strings
            - single space-delimited string
- tuples as immutable lists
    - see table for list of similar functionality

## Slicing
- slices and range use [0,n) range functionality
    - easy to see length of a slice or range when only the stop position is given
        - `range(3)` and `list[:3]` produce three items
    - easy to compute the length of a slice or range when start and stop are given
        - subtract stop - start
    - easy to split a sequence in two parts at any index x without overlap
        - `list[:x] and list[x:]`
- stride for third element causes skip or items in reverse
    - uses `slice(start, stop, step)` object and a seq calls `seq.__getitem__(slice(start, stop, step))`
- can use slice objects (notation for `seq[start:stop:step]` is only valid in `[]`)
```
s1 = slice(0, 6)
elems = items[s1]
```
- `[]` operator can take multiple indices or slices separated by comma
    - can use with numpy.ndarray to get 2-dim slices
    - calls `a.__getitem__(( i, j))` to evaluate `a[i, j]`
- `...` is a token and is an Ellipsis object - can be used to represent `[:]` in
  intermediate dimensions of a multi-dimensional array/list

- slices can be used to assign/modify and delete (using `del`)
```
l = list(range(10))
l[2:5] = [20, 30]
del l[5:7]
l[3::2] = [11, 22]
l[2: 5] = 100  # error - needs iterable
l[2:5] = [100]  # just assigns 100 to index 2
```

## Using + and * with Sequences
- operands to + and * for sequences must be of the same sequence type and produce
  a new sequence without modifying the originals
- expressions like a * n when a is a sequence containing mutable items have unexpected results
    - trying to initialize a list of lists as `l = [[]] * 3` results in a list with
      three references to the same inner list
- list of lists
```
# proper method
[['_'] * 3 for i in range(3)]

# equivalent to
board = []
for i in range(3):
    row = ['_'] * 3
    board.append(row)



# list with references to the same lists
[['_'] * 3] * 3

# equivalent to
row = ['_'] * 3
board = []
for i in range(3):
    board.append(row)
```

## Augmented Assignment with Sequences
- += and *= operators behave differently depending on the mutability of the first
  argument
- special method for += is `__iadd__` (in-place add) with a fallback to `__add__`
  when it isn't implemented
- for mutable sequences `__iadd__` is most likely implemented and += most likely happens
  in place
    - in place is not possible for immutable sequences
- *= -> `__imul__`
    - for mutable - after multiplication the sequence is the same object with items
      appended
    - for immutable - after multiplication a new sequence is created
- repeated concatenation of immutable sequences is inefficient because of a large
  number of copies in the interpreter

- NOTE: can use dis module to disassemble Python code (for CPython)!!!
- corner case example lessons
    - do not put mutable items in tuples
    - augmented assignment is not an atomic operation
        - can throw an exception after doing part of the operation
    - can easily inspect Python bytecode
        - often helpful to see what is going on under the hood

## list.sort and the sorted Built-In Function
- list.sort sorts in place and returns None
    - convention - functions or methods that modify in-place should return None
    - cannot cascade calls to these methods
        - see Wiki Fluent Interface
- sorted creates a new list and returns it
    - accepts any iterable object and always returns a sorted list
- both list.sort and sorted take two optional keyword args
    - reverse - True or False for reversed order sort
    - key - one arg function used to produce the sorting key (default is compare items)
        - can also be used with the min() and max() built-ins and more

## Managing Ordered Sequences with bisect
- standard binary search in `bisect` module
    - haystack - first arg and sorted sequence (ascending order)
    - needle - second arg to search for
- can use the result of bisect(haystack, needle) as the index argument to
  haystack.insert( index, needle)
    - using insort does both steps, and is faster
- see https://code.activestate.com/recipes/577197-sortedcollection/ for an
  easy to use sorted collection
- can fine-tune bisect behavior
    - pair of optional arguments lo and hi can be used to narrow the region in the
      sequence to be searched when inserting
        - lo defaults to 0
        - hi to len() of the sequence
     - bisect is an alias for bisect_right
        - sister function called bisect_left
        - difference is apparent only when the needle compares equal to an item in
          the list
            - bisect_right returns an insertion point after the existing item
            - bisect_left returns the position of the existing item so insertion
              would occur before it
            - if the sequence contains objects that are distinct yet compare equal,
              then it may be relevant
- bisect.insort(seq, item) can be used to insert new items in a sorted sequence in
  sorted order
    - takes optional lo, hi
    - also has insort_left

## When a List is Not the Answer
- better options than list for specific use cases
- array is better for storing large amounts of numeric data
    - does not hold full numeric objects
    - holds packed byte representation of values
- collections.deque is better for constantly adding and removing data from the
  ends of a sequence
- array supports all mutable sequence types and additional methods for fast
  loading (`.frombytes` and `.tofile`)
    - as minimal as a C-style array
- `.tofile` and `.fromfile` are very fast (nearly 60x and 7x) and saves a large amount
  of disk memory
- can use pickle also (not as fast for numeric data, but handles almost all built-in
  types)
- use bytes and bytearray for numeric arrays that represent binary data (images, etc)
- see Table 2-2 for comparison of the features of list and array.array
- array does not have an `array.sort` - use the sorted built-in and bisect.insort
```
a = array.array( a.typecode, sorted( a))
```

- built-in memoryview class
    - shared-memory sequence type that allows handling slices of arrays without copying bytes
    - essentially a generalized NumPy array structure in Python itself (without the math)
    - allows sharing memory between data-structures (things like PIL images, SQLlite databases,
      NumPy arrays, etc.) without copying
    - very important for large data sets
    - memoryview.cast method allows changing the way multiple bytes are read or written as units
      without moving bits around (just like the C cast operator)
        - returns another memoryview object, always sharing the same memory
```
numbers = array.array('h', [-2, -1, 0, 1, 2])
memv = memoryview(numbers)
len(memv) 5
memv[0] -2
memv_oct = memv.cast('B')
```

- use numpy and scipy for complex numerical/scientific operations on sequences types
    - `numpy.ndarray`

- can use a list as a stack or queue using `.append` or `.pop` but inserting or
  removing from the left (0 index) of a list is costly
    - use collections.deque
- deque is thread-safe and double-ended
    - can also be bounded
    - implements most of the list methods, and adds a few specific to its design, like
      popleft and rotate
    - hidden cost - removing items from the middle of a deque is not as fast
        - optimized for appending and popping from the ends
    - append and popleft are atomic so safe for multithreaded applications
- see table 2-3 for features of deque

- additional modules for list-like data structures
    - queue
        - provides the synchronized (i.e., thread-safe) classes Queue, LifoQueue, and PriorityQueue
        - used for safe communication between threads
        - can be bounded by providing a maxsize argument greater than 0 to the constructor
        - don’t discard items to make room as deque does
            - when the queue is full the insertion of a new item blocks — i.e., it waits until some other
              thread makes room by taking an item from the queue
            - useful to throttle the number of live threads
    - multiprocessing
        - bounded Queue (similar to queue.Queue) but designed for interprocess communication
        - specialized multiprocessing.JoinableQueue is also available for easier task management
    - asyncio
        - New to Python 3.4
        - provides Queue, LifoQueue, PriorityQueue, and JoinableQueue with APIs inspired by
          the classes contained in the queue and multiprocessing modules
        - adapted for managing tasks in asynchronous programming
    - heapq
        - heapq does not implement a queue class
        - provides functions like heappush and heappop
        - allow use of a mutable sequence as a heap queue or priority queue

# Ch. 3 - Dictionaries and Sets