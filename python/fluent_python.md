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
- much of the Python engine is implemented using dictionaries under the hood
- hash tables are used to implement Python dicts

## Generic Mapping Types
- `collections.abc` provides Mapping and MutableMapping
```
MutableMapping -> Mapping -> Container
                          -> Iterable
                          -> Sized
```
- custom mapping types often extend dict or `collections.UserDict`
- best to check with
```
isinstance(instance, abc.Mapping)
```
- all std lib mapping types use dict and so keys must be hashable
- an object is hashable if it
    - has a hash value which never changes during its lifetime
        - needs a __hash__() method
    - can be compared to other objects
        - needs an __eq__() method
    - hashable objects which compare equal must have the same hash value
- atomic immutable types (str, bytes, numeric types) are all hashable
- frozenset is always hashable, because its elements must be hashable by definition
- tuple is hashable only if all its items are hashable
    - other than that, all immutable built-in objects are hashable
- user-defined types are hashable by default because their hash value is their id()
  and they all compare not equal
    - if an object implements a custom __eq__ that takes into account its internal
      state, it may be hashable only if all its attributes are immutable

## dict Comprehensions
- dictcomp builds a dict instance by producing key:value pair from any iterable
```
d = {key: val for key_v, value_v in seq}
```

## Overview of Common Mapping Methods
- dict, defaultdict and OrderedDict (in collections)
- see table 3-1

- setdefault
    - dict access with `d[k]` raises an error when k is not an existing key
    - `d.get(k, default)` is an alternative to `d[k]` when a default value is more
      convenient than handling KeyError
    - when updating the value found (if it is mutable), using
      either `__getitem__` or `get` is awkward and inefficient
- setdefault only performs one lookup whereas `if key not in d:` performs 2 or 3 lookups
```
# prefer
d.setdefault(key, val)

# over
if key not in d:
```

## Mappings with Flexible Key Lookup
- defaultdict is configured to create items on demand whenever a missing key is searched
    - on creation, user provides a callable that is used to produce a default value
      when `__getitem__` is passed a nonexistent key argument
    - e.g. `dd = defaultdict(list)` if 'new_key' is not in dd, `dd['new_key']`
        - calls list() to create a new list
        - inserts the list into dd using 'new_key' as key
        - returns a reference to that list
    - callable that produces the default values is held in an instance attribute called
      default_factory
    - uses `__missing__` to call default_factory
- `__missing__` special method
    - not defined in the base dict class
    - subclass dict and provide a `__missing__` method, the standard `dict.__getitem__` will
      call it whenever a key is not found, instead of raising KeyError
    - just called by `__getitem__` (i.e., for the `d[k]` operator)
        - presence of a `__missing__` method has no effect on the behavior of other
          methods that look up keys, such as get or `__contains__` (which implements
          the in operator)
        - why default_factory of defaultdict works only with `__getitem__`
- `k in my_dict.keys()` is efficient in Python 3 even for very large mappings because
  dict.keys() returns a view (similar to a set)
    - containment checks in sets are as fast as in dictionaries
        - see "Dictionary" view objects section of the documentation
    - in Python 2, dict.keys() returns a list which is not efficient for large
      dictionaries, because k in my_list must scan the list

## Variations of dict
- collections.OrderedDict
    - maintains keys in insertion order, allowing iteration over items in a predictable order
    - popitem method of an OrderedDict pops the last item by default, but if called as
      my_odict.popitem(last = False), it pops the first item added
- collections.ChainMap
    - holds a list of mappings that can be searched as one
    - lookup is performed on each mapping in order, and succeeds if the key is found
      in any of them
    - useful to interpreters for languages with nested scopes, where each mapping represents
      a scope context
- collections.Counter
    - mapping that holds an integer count for each key
    - updating an existing key adds to its count
    - can be used to count instances of hashable objects (the keys) or as a multiset — a set
      that can hold several occurrences of each element
    - implements the + and - operators to combine tallies
    - has other useful methods such as most_common([n])
        - returns an ordered list of tuples with the n most common items and their counts
- collections.UserDict
    - pure Python implementation of a mapping that works like a standard dict

## Subclassing UserDict
- prefer over dict
    - built-in dict has methods that are shortcuts
    - corresponding methods in UserDict work without begin overridden
- UserDict does not inherit from dict but uses dict for internal data attribute
    - undesired recursion when coding special methods like `__setitem__`
    - simplifies coding of `__contains__`
- methods to note
    - `__missing__`
    - `__contains__`
    - `__setitem__`
    - `MutableMapping.update`
    - `Mapping.get`
- see PEP 455 and TransformDict as well

## Immutable Mappings
- Python 3.3 - `types.MappingProxyType` returns `mappingproxy` which is a dynamic
  read-only view into a mapping type

## Set Theory
- set and frozenset - unique collections of objects
    - elements must be hashable
    - set is not hashable
    - frozenset is hashable so a set may contain frozensets
    - implement the set operations as infix operators
        - given two sets a and b
        - a | b returns their union
        - a & b computes the intersection
        - a - b the difference
    - use of set operations can reduce both the line count and the runtime of Python programs
- can write set literals as `{1, 2, 3}` but must use `set()` for an empty set
    - `{}` is an empty dict
    - set literal construction is faster and more readable than calling the constructor
- frozenset must be constructed with the constructor
```
frozenset([1, 2, 3, 4])
```

- setcomps can be constructed as dictcomps, using `{}`
- see table 3-2 for set operations and methods

## dict and set Under the Hood
- dicts and sets are fast
    - searches are fastest in set, then dict, then list
- C code has many optimizations
- hash table
    - sparse array (i.e., an array that always has empty cells)
        - cells in a hash table are often called "buckets"
    - dict hash table
        - bucket for each item, and it contains two fields
            - a reference to the key
            - a reference to the value of the item
        - all buckets have the same size
        - access to an individual bucket is done by offset
    - Python tries to keep at least 1/ 3 of the buckets empty
        - if too dense, copied to resize
    - for insert, the first step is to calculate the hash value of the item key
        - done with the hash() built-in function
- hash() built-in function works directly with built-in types and falls back to calling
  `__hash__` for user-defined types
    - if two objects compare equal, their hash values must also be equal
    - otherwise the hash table algorithm does not work
    - starting with Python 3.3, a random salt value is added to the hashes of str, bytes,
      and datetime objects
        - salt value is constant within a Python process but varies between interpreter runs
        - random salt is a security measure to prevent a DOS attack
- for get
    - Python calls `hash(search_key)` to obtain the hash value of search_key and uses
      the least significant bits of that number as an offset to look up a bucket in the
      hash table
    - the number of bits used depends on the current size of the table
    - if the found bucket is empty, KeyError is raised
    - otherwise, the found bucket has an item
        - then Python checks whether search_key == found_key
        - on match the found value is returned
        - if search_key and found_key do not match, this is a hash collision
            - happens because a hash function maps arbitrary objects to a small number of
              bits and the hash table is indexed with a subset of those bits
        - to resolve the collision
            - algorithm then takes different bits in the hash
            - massages them in a particular way, and uses the result as an offset to look up
              a different bucket
            - if empty KeyError is raised
            - otherwise return item value or repeat collision resolution
    - same process for insert or update
- consequences
    - keys must be hashable objects
        - supports the hash() function via a `__hash__()` method that always returns the
          same value over the lifetime of the object
        - supports equality via an `__eq__()` method
        - if a == b is True then hash(a) == hash( b) must also be True
    - dicts have significant memory overhead
        - must be sparse
        - better to use list of tuples or named tuples
            - removes overhead of one hash table per record
            - does not store each field name again with each record
    - key search is very fast
        - trades space for time
    - key ordering depends on insertion order
        - when a hash collision happens, the second key ends up in a position that it would not
          normally occupy if it had been inserted first
        - dict built as dict([(key1, value1), (key2, value2)]) compares equal to dict([(key2, value2), (key1, value1)])
        - key ordering may not be the same if the hashes of key1 and key2 collide
    - adding items to a dict may change the order of existing keys
- practical consequences of sets
    - also use hash tables
    - dict consequences above apply
        - set elements must be hashable objects
        - sets have a significant memory overhead
        - membership testing is very efficient
        - element ordering depends on insertion order
        - adding elements to a set may change the order of other elements


# Ch. 4 - Text versus Bytes
- definition of character complicates definition of string
    - character is generally Unicode character
    - Python 3
        - `bytes` is raw bytes
        - `str` is Unicode chars
    - Python 2
        - `str` is raw bytes
        - `unicode` obj is Unicode chars
- Unicode standard
    - identity of a character (code point)
        - number from 0 to 1,114,111 (base 10)
        - shown in the Unicode standard as 4 to 6 hexadecimal digits with a "U +" prefix
        - about 10% of the valid code points have characters assigned to them in Unicode
          6.3, the standard used in Python 3.4
    - encoding is an algorithm that converts code points to byte sequences and vice versa
        - actual bytes that represent a character depend on the encoding in use
        - code point for A (U + 0041) is encoded as the single byte \x41 in the UTF-8 encoding,
    - converting from code points to bytes is encoding
    - converting from bytes to code points is decoding
- Python 3 str is pretty much the Python 2 unicode type
- Python 3 bytes is not the old str renamed
    - closely related bytearray type

## Byte Essentials
- there are two basic built-in types for binary sequences
    - immutable bytes type introduced in Python 3
    - mutable bytearray, added in Python 2.6
    - Python 2.6 also introduced bytes
        - just an alias to the str type
        - does not behave like the Python 3 bytes type
- each item in bytes or bytearray is an integer from 0 to 255
    - not a one-character string like in the Python 2 str
- a slice of a binary sequence always produces a binary sequence of the same type
- `b[0]` returns an int but `b[:1]` returns a bytes object of length 1
    - only sequence type where `s[0] == s[:1]` is the str type
    - for every other sequence, `s[i]` returns one item, and `s[i:i + 1]` returns a
      sequence of the same type with the `s[i]` item inside it
-  are really sequences of integers, their
- literal notation of binary sequences (as integers) reflects the fact that
  ASCII text is often embedded in them
    - three different displays are used, depending on each byte value
        - bytes in the printable ASCII range the ASCII character itself is used
        - bytes corresponding to tab, newline, carriage return, and \, the escape
          sequences \t, \n, \r, and \\ are used
        - for every other byte value, a hexadecimal escape sequence is used
          (e.g., \x00 is the null byte)
- both bytes and bytearray support every str method
    - except formatting (format, format_map)
    - others that depend on Unicode data, including casefold, isdecimal, isidentifier, isnumeric, isprintable,
      and encode
- regular expression functions in the re module also work on binary sequences, if the regex is compiled from
  a binary sequence instead of a str
- % operator does not work with binary sequences in Python 3.0 to 3.4 but is beyond
- has `.fromhex` method
- means of construction
    - str and an encoding keyword argument
    - iterable providing items with values from 0 to 255
    - single integer, to create a binary sequence of that size initialized with null bytes
      (deprecated in Python 3.5 and removed in Python 3.6)
    - an object that implements the buffer protocol (e.g., bytes, bytearray, memoryview, array.array)
        - copies the bytes from the source object to the newly created binary sequence

- struct module provides functions to parse packed bytes into a tuple of fields of different types and to
  perform the opposite conversion, from a tuple into packed bytes
    - used with bytes, bytearray, and memoryview objects
- memoryview class does not allow creation or storage of byte sequences
    - provides shared memory access to slices of data from other binary sequences, packed arrays,
      and buffers such as Python Imaging Library (PIL) images, without copying the bytes
    - slicing returns a new memoryview without copying bytes
- NOTE: use mmap for memory-mapped files

## Basic Encoders/Decoders
- bundles more than 100 codecs with aliases
- latin1 a.k.a. iso8859_1
    - important because it is the basis for other encodings, such as cp1252 and Unicode itself
      (note how the latin1 byte values appear in the cp1252 bytes and even in the code points)
- cp1252
    - a latin1 superset by Microsoft
    - adds useful symbols like curly quotes and the € (euro)
    - some Windows apps call it "ANSI"
    - never a real ANSI standard
- cp437
    - original character set of the IBM PC, with box drawing characters
    - incompatible with latin1, which appeared later
- gb2312
    - legacy standard to encode the simplified Chinese ideographs used in mainland China
    - one of several widely deployed multibyte encodings for Asian languages
- utf-8
    - most common 8-bit encoding on the Web
   - backward-compatible with ASCII (pure ASCII text is valid UTF-8)
- utf-16le
    - one form of the UTF-16 16-bit encoding scheme
    - all UTF-16 encodings support code points beyond U + FFFF through escape sequences called "surrogate pairs"

## Understanding Encode/Decode Problems
- generic UnicodeError exception
- reported error is almost always more specific
    - UnicodeEncodeError - when converting str to binary sequences
    - UnicodeDecodeError - when reading binary sequences into str
- can get a SyntaxError when loading Python modules when the source encoding is
  unexpected
  . We’ll show how to handle all of these errors in the next sections.
  Tip The first thing to note
- when receiving a Unicode error, note the exact type of the exception
- dealing with UnicodeEncodeError
    - most non-UTF codecs handle only a small subset of the Unicode characters
    - can use `error='ignore'` (generally bad to do) in encode call
    - can use `error='replace'` or `errors =' xmlcharrefreplace'` to automatically replace
      in encode call
- dealing with UnicodeDecodeError
    - not every byte holds a valid ASCII character
    - not every byte sequence is valid UTF-8 or UTF-16
    - many legacy 8-bit encodings like 'cp1252', 'iso8859_1', and 'koi8_r' are able to decode any
      stream of bytes, including random noise, without generating errors
    - if the program assumes the wrong 8-bit encoding, it will silently decode garbage
    - can also pass `errors='replace'` here
- dealing with SyntaxError when loading modules with unexpected encoding
    - UTF-8 is the default source encoding for Python 3
    - ASCII was the default for Python 2 (starting with 2.5)
    - loading a .py module containing non-UTF-8 data and no encoding declaration
    - UTF-8 is widely deployed in GNU/Linux and OSX systems
        - possible to open a .py file created on Windows with cp1252
    - error happens in Python for Windows, because the default encoding for Python 3 is UTF-8 across all platforms
    - add a magic coding comment at the top of the file to fix this problem
      `# coding: cp1252`
    - Python 3 source code is no longer limited to ASCII and defaults to the excellent UTF-8 encoding, the best “fix” for source code in legacy encodings like 'cp1252' is to convert them to UTF-8 already, and not bother with the coding comments. If your editor does not support UTF-8, it’s time to switch.
- must be told what the encoding of a byte sequence is
    - can use libraries like Chardet to determine within a confidence level
- BOM - byte order mark or bytes denoting endianness of CPU where encoding was performed

## Handling Text Files
- best practice for handling text
    - bytes should be decoded to str as early as possible on input (e.g., when opening a file for reading)
    - text handling should be done exclusively on str objects within business logic of program
        - never encode or decode in the middle of other processing
    - str objects should be encoded to bytes as late as possible on output
- in Python 3, open built-in handles the decoding and encoding on read and write
- code that has to run on multiple machines should never depend on encoding defaults
    - always pass an explicit encoding argument when opening text files
    - default may change from one machine to the next
- do not open text files in binary mode unless analyzing the file contents to determine
  encoding
    - prefer Chardet for analyzing the the file contents

- encoding defaults
```
locale.getpreferredencoding()
type(my_file)
my_file.encoding
sys.stdout.isatty()
sys.stdout.encoding
sys.stdin.isatty()
sys.stdin.encoding
sys.stderr.isatty()
sys.stderr.encoding
sys.getdefaultencoding()
sys.getfilesystemencoding()
```
- Linux and macOS returned same results, Windows was very different
    - Linux and macOS are set to UTF-8 by default
    - locale.getpreferredencoding() is the most important setting
    - text files use locale.getpreferredencoding() by default
    - output is going to the console, so sys.stdout.isatty() is True
        - sys.stdout.encoding is the same as the console encoding
- if the encoding argument is omitted when opening a file, default is given by
  locale.getpreferredencoding()
- encoding of sys.stdout/stdin/stderr is given by PYTHONIOENCODING env var
    - if not set, inherited from console or defined by locale.getpreferredencoding()
      if IO is going to/from a file
- sys.getdefaultencoding() is used internally to convert binary to/from str
    - setting cannot be changed
- sys.getfilesystemencoding() is used to encode/decode filenames (but not file contents)
- NOTE: do not rely on encoding defaults!!!

## Normalizing Unicode for Saner Comparisons
- Unicode has combining chars like diacritics
- use unicodedata.normalize
    - on of four strings 'NFC', 'NFD', 'NFKC', 'NFKD' as first arg
    - normalization Form C (NFC) composes the code points to produce the shortest
      equivalent string
    - NFD decomposes, expanding composed characters into base characters and separate
      combining characters
    - NFKC and NFKD — the letter K stands for "compatibility"
        - stronger forms of normalization, affecting "compatibility characters"
        - some Unicode characters appear more than once for compatibility with preexisting standards
        - each compatibility character is replaced by a "compatibility decomposition" of one or
          more characters that are considered a preferred representation
        - done even if there is some formatting loss
    - use NFKC and NFKD only for special cases like search and indexing because they cause
      data loss

- can use `str.casefold()` which returns different characters for 116 of 110,122 named
  characters in Unicode 6.3

```
from unicodedata import normalize
def nfc_equal(str1, str2):
    return normalize('NFC', str1) == normalize('NFC', str2)

def fold_equal(str1, str2):
    return (normalize('NFC', str1).casefold() == normalize('NFC', str2).casefold())
```

- Google search ignores diacritics in certain contexts
```
def remove_diacritics(txt):
    norm_txt = unicodedata.normalize('NFD', txt)
    shaved = ''.join(c for c in norm_txt if not unicodedata.combining(c))
    return unicodedata.normalize('NFC', shaved)
```
- see text for more moderate examples
    - different languages have different rules for removing diacritics

## Sorting Unicode Text
- Python sort compares items one-by-one, e.g. code points for strings
    - causes issues when using non-ASCII chars
- use local.strxfrm to make local-aware comparisons
    - must first set the local using `local.setlocal()`
    - calling setlocale in a library is not recommended because locale settings are global
        - application or framework should set the locale when the process starts
        , and should not change it afterwards.
    - locale must be installed on the OS, otherwise setlocale raises a locale.Error: unsupported
    - local name must be spelled correctly
        - They are pretty much
        - standardized in the Unix derivatives as 'language_code.encoding',
        - syntax is more complicated on Windows - Language Name-Language Variant_Region Name.codepage
    - local must be correctly implemented by OS (incorrect on macOS)
- can use PyUCA

## The Unicode Database
- DB mapping code points to character names and metadata about relationships between characters
- `unicodedata` module

## Dual-Mode str and bytes APIs
- re and os modules (plus more) have functions that accept str or bytes
- \d, \w, etc used in regexes with bytes only match ASCII
    - when used with str matches Unicode digits and letters beyond ASCII
- many filenames on Linux may have byte sequences that cannot be decoded to str
    - common on servers with clients from a variety of OSes
- all os module functions that accept filenames or pathnames take arguments as str or bytes
    - called with a str argument
        - argument will be automatically converted using the codec named by sys.getfilesystemencoding()
        - OS response will be decoded with the same codec
    - can pass bytes arguments to the os functions to get bytes return values
- fsencode(filename)
    - encodes filename (can be str or bytes) to bytes using the codec named by sys.getfilesystemencoding()
     if filename is of type str, otherwise returns the filename bytes unchanged
- fsdecode(filename)
    - decodes filename (can be str or bytes) to str using the codec named by sys.getfilesystemencoding() if
      filename is of type bytes, otherwise returns the filename str unchanged
      . On Unix-derived platforms,
- these functions use the surrogateescape error handler to avoid issues with unexpected bytes on Unix systems
- strict error handler is used on Windows
