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
- hash tables are the engines behind Python's high-performance dicts

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
      (it needs a `__hash__()` method), and can be compared to other objects (it needs an __eq__() method)
    - hashable objects which compare equal must have the same hash value
    - the atomic immutable types (str, bytes, numeric types) are all hashable
    - a frozenset is always hashable, because its elements must be hashable by definition
    - a tuple is hashable only if all its items are hashable
    - user-defined types are hashable by default because their hash value is their id
      () and they all compare not equal
        - if an object implements a custom `__eq__` that takes into account its internal state
          it may be hashable only if all its attributes are immutable

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
        - don't discard items to make room as deque does
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
  . We'll show how to handle all of these errors in the next sections.
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
    - Python 3 source code is no longer limited to ASCII and defaults to the excellent UTF-8 encoding, the best "fix" for source code in legacy encodings like 'cp1252' is to convert them to UTF-8 already, and not bother with the coding comments. If your editor does not support UTF-8, it's time to switch.
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

# Ch. 5 - First-Class Functions
- first-class object is a program entity that can be
    - created at runtime
    - assigned to a variable or element in a data structure
    - passed as an argument to a function
    - returned as the result of a function

## Treating a Function Like an Object
- can use `__doc__` attribute for help text and call `help(func)`
- functions also have other accessible attributes
- can assign functions to vars and call
- can pass as args to other functions

## Higher-Order Functions
- function that takes other functions as arguments, e.g. map, sorted
- functional paradigm
    - map
    - filter
    - reduce
    - apply (deprecated in favor of *args and **kwargs)
- prefer listcomps, gencomps, etc over map and filter
- map & filter return generators in Python 3
- reduce was moved from built-in to functools
    - prefer sum
- all(iterable)
    - returns True if every element of the iterable is truthy
    - `all([])` returns True
- any(iterable)
    - returns True if any element of the iterable is truthy
    - `any([])` returns False

## Anonymous Functions
- use lambda
    - body must be pure, e.g. cannot make assignments or use statements such as
      while, try, etc.
- rarely useful
- see https://docs.python.org/3/howto/functional.html

## The Seven Flavors of Callable Objects
- use the `callable()` built-in to determine if an object is callable
- callable types
    - user-defined functions
        - created with def statements or lambda expressions
    - built-in functions
        - a function implemented in c (for CPython), like len or time.strftime
    - built-in methods
        - methods implemented in C, like dict.get. methods functions defined in the
          body of a class
    - classes
        - when invoked, a class runs its __new__ method to create an instance, then __init__ to initialize it, and
          finally the instance is returned to the caller
        - because there is no new operator in python, calling a class is like calling a function
           (usually calling a class creates an instance of the same class, but other behaviors are
           possible by overriding __new__)
    - class instances
        - if a class defines a __call__ method, then its instances may be invoked as functions
    - generator functions
        - functions or methods that use the yield keyword
        - when called, generator functions return a generator object
        - generator functions are unlike other callables in many respects

## User-Defined Callable Types
- instances of Python objects can be made to behave like functions by implementing
  a `__call__` instance method
- good for storing state across invocations
- also useful for decorators that need to store state (for example memoization)
- see also closures

## Function Introspection
- use `dir` built-in to see attributes
- uses `__dict__` to store user attributes assigned to it
- not common to assign attributes to functions, but is a practice used by Django
- attributes specific to functions

| Name | Type | Description |
| ---- | ---- | ----------- |
| __annotations__ | dict | parameter and return annotations |
| __call__ | method-wrapper | implementation of the () operator; a.k.a. the callable object protocol |
| __closure__ | tuple | the function closure, i.e., bindings for free variables (often is None) |
| __code__ | code | function metadata and function body compiled into bytecode |
| __defaults__ | tuple | default values for the formal parameters |
| __get__ | method-wrapper | implementation of the read-only descriptor protocol |
| __globals__ | dict | global variables of the module where the function is defined |
| __kwdefaults__ | dict | default values for the keyword-only formal parameters |
| __name__ | str | the function name |
| __qualname__ | str | the qualified function name, e.g., Random.choice (see PEP-3155) |

## From Positional to Keyword-Only Parameters
- keyword only args and * and ** to "explode" args
- prefixing an argument with * captures any number of arguments as a tuple
- prefixing an argument with ** captures any number of arguments as a dict
- can support keyword only positional arguments with f(a, *, b)
    - keyword-only positional arguments can be required by not defining a default
      value for it

## Retrieving Information About Parameters
- `__defaults__` attribute holds a tuple with the default values of positional and keyword
  arguments
- defaults for keyword-only arguments appear in `__kwdefaults__`
- names of the arguments are found within the `__code__` attribute
    - a reference to a code object with many attributes of its own
- `__defaults__`, `__code__.co_varnames`, and `__code__.co_argcount`
- see examples 5-16 and 5-17 for extracting function arg info
- inspect.signature returns an inspect.Signature object
    - has a parameters attribute
        - can read an ordered mapping of names to inspect.Parameter objects
    - each Parameter instance has attributes such as name, default, and kind
    - special value inspect._empty denotes parameters with no default
    - kind attribute holds one of five possible values from the _ParameterKind class
        - POSITIONAL_OR_KEYWORD
            - a parameter that may be passed as a positional or as a keyword argument
            (most Python function parameters are of this kind).
        - VAR_POSITIONAL
            - a tuple of positional parameters
        - VAR_KEYWORD
            - a dict of keyword parameters
        - KEYWORD_ONLY
            - a keyword-only parameter
            (new in Python 3).
        - POSITIONAL_ONLY
            - a positional-only parameter
            - currently unsupported by Python function declaration syntax, but
              shown by existing functions implemented in C — like divmod — that do
              not accept parameters passed by keyword
    - also has an annotation attribute that is usually inspect._empty but
      may contain function signature metadata provided via the new
      annotations syntax in Python 3

## Function Annotations
- Python 3 special syntax to attach metadata to the parameters of a function
  declaration and its return value
- annotation goes between the argument name and the = sign with default values
- add -> and another expression between the ) and the : at the tail of the function declaration
  to annotate the return value
- expressions may be of any type
- most common types used in annotations are classes, like str or int, or strings, like
  'int > 0'
- no processing is done with annotations
    - merely stored in the __annotations__ attribute of the function
- only thing Python does with annotations is to store them in the `__annotations__` attribute of
  the function
    - annotations have no meaning to the Python interpreter
    - metadata that may be used by tools, such as IDEs, frameworks, and decorators

## Packages for Functional Programming
- operator module
    - functions for common operators, functions to pick items from sequences, read
      attributes from object (itemgetter, attrgetter)
- functools
    - reduce
    - partial
        - allows partial application of a function
        - takes a callable and keyword and positional arguments to bind
    - partialmethod
        - same as partial but used with methods


# Ch. 6 - Design Patterns with First-Class Functions
- design patterns can be drastically modified or obsolete in dynamic languages

## Case Study: Refactoring Strategy
- classic pattern performs actions based on the specific instance of a polymorphic type
  passed and a method called on that instance
- can easily replace with functions when functions are first-class
- skipping material here
- NOTE: simplest way to declare an ABC is to subclass abc.ABC in Python 3.4
    - must use the metaclass = keyword in the class statement from Python 3.0 to 3.3
    - e.g., class Promotion(metaclass = ABCMeta):
- modules are also first-class
    - can encapsulate strategies in modules
- use the inspect module for introspection of modules

## Command
- decouple an object that invokes an operation (the Invoker) from the provider object
  that implements it (the Receiver)
    - each invoker is a menu item in a graphical application, and the receivers are
      the document being edited or the application itself
    - put a Command object between the Invoker and Receiver
    - Command implements an interface with a single method, execute, which calls
      some method in the Receiver to perform the desired operation
    - Invoker does not need to know the interface of the Receiver
    - different receivers can be adapted through different Command subclasses
    - Invoker is configured with a concrete command and calls its execute method to
      operate it
    - may store sequence of commands
    - "Commands are an object-oriented replacement for callbacks"
- to simplify the pattern, can simply give the Invoker a function instead of a Command instance
    - Invoker can just call command() instead of calling command.execute()
- more advanced uses of the Command pattern may need more than a simple callback function
- Python alternatives
    - a callable instance can keep whatever state is necessary, and provide extra methods in addition to `__call__`
    - a closure can be used to hold the internal state of a function between calls

# Ch. 7 - Function Decorators and Closures
## Decorators 101
- callable that takes another function as argument (the decorated function)
    - performs processing with the decorated function
    - returns function or replaces it with another function or callable object
```
@decorate
def target():
    print('running target()')

# equivalent to

def target():
    print('running target()')

target = decorate(target)
```
- just syntactic sugar
- usually executed immediately when a module is loaded

## When Python Executes Decorators
- decorators run after decorated function is defined - usually at import time
- decorated function only run when explicitly invoked
- import time vs. runtime

## Decorator-Enhanced Strategy Pattern
- cleans up over previous examples
    - do not need to use special names
    - makes it easier to modify all decorated functions at once and highlight purpose
    - can define decorated functions within any module

## Variable Scope Rules
- variables within a function that are ASSIGNED (important) to and not explicitly declared
  global are designated as local variables when Python compiles the body of the function
    - can be observed in generated byte code
- Python does not require variables to be declared
    - assumes that a variable assigned in the body of a function is local
- use the global declaration to treat a global variable as global

## Closures
- closures are sometimes confused with anonymous functions
    - the use of anonymous functions is what historically led to the definition of functions
      inside functions
    - closures are important when using nested functions
- a closure is a function with an extended scope that encompasses nonglobal
  variables referenced in the body of the function but not defined there
    - inconsequential whether function is anonymous or not
    - important aspect is whether function can access nonglobal variables that are
      defined outside of its body
- free variable - technical term meaning a variable that is not bound in the local scope
    - often used in closures to reference variables in the enclosing function
    - can inspect free variables in `__code__.co_freevars`
- summary
    - a closure is a function that retains the bindings of the free variables that exist
      when the function is defined
    - free variables can then be used later when the function is invoked and the defining
      scope is no longer available

## The nonlocal Declaration
- `count += 1` is actually `count = count + 1` (when count is a number or immutable type)
  so the variable becomes local
- can only read to free vars that are immutable types (numbers, strings, tuples, etc)
    - update/assignment implicitly creates a local variable
- `nonlocal` keyword added in Python 3 to tag free variables
    - to handle the lack of nonlocal in Python 2
        - store the variables the inner functions need to change (e.g., count, total)
          as items or attributes of some mutable object, like a dict or a simple instance,
          and bind that object to a free variable
        - see third code snippet of PEP 3104

## Implementing a Simple Decorator

## Decorators in the Standard Library
- three built-in decorators - property, classmethod, staticmethod
- functools.wraps helps to build well-behaved decorators
- functools.lru_cache
    - implements memoization - optimization technique that works by saving the results of
      previous invocations of an expensive function, avoiding repeat computations on previously used
      arguments
    - uses an LRU cache
- functools.singledispatch
    - new decorator in Python 3.4
    - transforms a function into a single-dispatch generic function
    - to define a generic function, decorate it with the @singledispatch decorator
        - dispatch happens on the type of the first argument
    - use the register() attribute of the generic function to add overloaded implementations to the function
        - it is a decorator and takes a type parameter and decorating a function implementing the operation for that type
    - decorating a plain function causes it to become a generic function or a group of functions to perform
      the same operation in different ways, depending on the type of the first argument
    - singledispatch package is available on PyPI as a backport compatible with Python 2.6 to 3.3
    - register the specialized functions to handle ABCs (abstract classes) instead of concrete implementations when possible
        - allows your code to support a greater variety of compatible types
       - allows code to support existing or future classes that are either actual or virtual
         subclasses of those ABCs
    - can register specialized functions anywhere in the system in any module
    - offers much more functionality (see PEP 443)
    - @singledispatch is not designed to bring Java-style method overloading to Python
        - a single class with many overloaded variations of a method is better than a
          single function with many if/else blocks
        - both are flawed because they concentrate too much responsibility in a single code unit (class or function)
        - the advantage of @singledispath is support of modular extension
            - each module can register a specialized function for each type it supports
- single dispatch
    - form of generic function dispatch where the implementation is chosen based on the type of
      a single argument
- generic function
    - function composed of multiple functions implementing the same operation for different
      types
        - the implementation that should be used during a call is determined by the dispatch algorithm

## Stacked Decorators
```
@d1
@d2
def f():
    print('f')

# equivalent to
def f():
    print('f')

f = d1(d2(f))
```

## Parameterized Decorators
- decorator takes decorated function as first arg
- can pass more args by making a decorator factory that takes args and
  returns a decorator which is then applied to the function in question
- see book code for examples


# Ch. 8 - Object References, Mutability, and Recycling
## Variables are Not Boxes
- conceptualize Python variables as labels and the label refers to an object
    - as opposed to a box in which objects/values are deposited and updated
- similar to Java references
- good to think of it as the variable is assigned to the object rather than
  the object is assigned to the variable
- always read the right-hand side first
    - rhs is where the object is created or retrieved
    - after that the variable on the left is bound to the object
    - similar to a label stuck to the object
- an object can have several labels assigned to it (aliasing)

## Identity, Equality, and Aliases
- every object has an identity, a type and a value
- an object's identity never changes once it has been created
- `is` operator compares the identity of two objects
- `id()` function returns an integer representing its identity
    - the meaning of an object's ID is implementation-dependent
    - id() returns the memory address of the object in CPython
    - may be something else in another Python interpreter
- ID is guaranteed to be a unique numeric label that will never change during the life of
  the object
- use of the `id()` function while programming is rare
    - identity checks are most often done with the `is` operator

### Choosing Between == and is
- the `==` operator compares the values of (data held by) objects
- `==` appears more frequently than `is` in Python code
- when comparing a variable to a singleton use `is`
- most common case is checking whether a variable is bound to None
    - use `x is None` or `x is not None`
    - `is` operator is faster than `==` because it cannot be overloaded
        - no need for interpreter to find and invoke special methods to evaluate it
        - computing is as simple as comparing two integer IDs
- `a == b` is syntactic sugar for `a.__eq__(b)`
    - `__eq__` method inherited from object compares object IDs, so it produces the
      same result as `is`
    - most built-in types override `__eq__` with more meaningful implementations that
      take into account the values of the object attributes
- equality checks may be expensive (collections, nested structures)

### The Relative Immutability of Tuples
- tuples hold references to objects
    - if the referenced items may change (if mutable) even if the tuple doesn't
    - immutability of tuples means the specific references contained in the tuple
      and not the references themselves
    - what never changes in a tuple is the identities of the items they contain

## Copies Are Shallow by Default
- easiest copy method for mutable collections is to use the built-in constructor
    - shortcut `l2 = l1[:]` also makes a copy
- produces a shallow copy where outermost container is duplicated but copy is filled
  with references to same items held by the original container
    - saves memory and doesn't not cause problems with immutable items
    - leads to issues with mutable items where aliases can cause unexpected modification

### Deep and Shallow
- copy modules provides `deepcopy` and `copy`
- copy.copy(x) - return a shallow copy of x
- copy.deepcopy(x) - return a deep copy of x
- both raise exception copy.error for module specific errors
- difference between shallow and deep copying is only relevant for compound objects
  (objects that contain other objects, like lists or class instances)
    - shallow copy constructs a new compound object and then inserts references into it
      to the objects found in the original (as able)
    - deep copy constructs a new compound object and recursively inserts copies into it
      of the objects found in the original
- deep copy problems
    - recursive objects (compound objects that, directly or indirectly, contain a reference to themselves)
      may cause a recursive loop
    - deep copy may copy too much
- deepcopy() avoids problems by
    - keeping a "memo" dictionary of objects already copied during the current copying
      pass
    - letting user-defined classes override the copying operation or the set of components
      copied
- can control the behavior of both copy and deepcopy by implementing the __copy__() and __deepcopy__()
  special methods as described in the copy module documentation

## Function Parameters as References
- Python passes parameters using call by sharing (e.g. pass by reference)
    - each formal parameter of the function gets a copy of each reference in the
    - parameters inside the function become aliases of the actual arguments
- actual arguments passed to functions may behave in different ways depending on
  the mutability of the arguments
    - with an assignment
        - number is unchanged
        - list is changed
        - tuple is unchanged
- avoid mutable objects as default values to function parameters
    - each default value is evaluated when the function is defined (usually on module load)
      and then become attributes of the function object
    - changes to the default value are shared across function calls
- carefully consider ownership and the resulting ability to modify a mutable variable
  passed to a function
    - with lists, for example, instead of using a default empty list, use None
        - first check for None and then create an empty list if None
        - otherwise construct a new list (or deepcopy) using the `list(arg)` ctor
    - avoids the potential to mutate the parameter unknowingly or share a default

## del and Garbage Collection
- objects are never explicitly destroyed
    - when they become unreachable they may be garbage-collected
- del statement deletes names, not objects
    - del command may cause an object to be garbage collected
        - if the variable deleted holds the last reference to the object
        - if the object becomes unreachable
- rebinding a variable may also cause the number of references to an object to reach
  zero
- `__del__` special method does not cause the disposal of the instance
    - should not be called by code
    - invoked by the Python interpreter when the instance is about to be destroyed
      to give it a chance to release external resources
    - rarely necessary to implement
- proper use of `__del__` is difficult
    - see the `__del__` special method documentation in "Data Model" Python Reference
    . In CPython, the
- primary algorithm for garbage collection in CPython is reference counting
    - each object keeps count of how many references point to it
    - object is destroyed when refcount reaches zero
        - `__del__` method is called (if defined)
        - memory allocated to the object is destroyed
        . In CPython 2.0,
- a generational garbage collection algorithm was added in CPython 2.0 to detect groups
  of objects involved in reference cycles which may be unreachable when all the mutual references
  are contained within the group
- other implementations of Python have more sophisticated garbage collectors that do not
  rely on reference counting
    - see "PyPy, Garbage Collection, and a Deadlock"
- REMEMBER: `del` does not delete objects but objects may be deleted after a call to
  `del` if the refcount drops to 0

## Weak References
- references keep an object alive in memory
    - garbage collector disposes of an object when the refcount drops to 0
- sometimes useful to have a reference to an object that does not increment refcount (extending lifetime)
    - common use case is a cache
    - don't want the cached objects to be kept alive just because they are referenced by the cache
- target of a reference is called the referent
- a weak reference does not prevent the referent from being garbage collected
- can use `weak_ref is None` to check if object is alive
- memory management under the hood often contains hidden implicit assignments that create
  new references
    - _ console variable
    - traceback objects
- weakref.ref class is a low-level interface intended for advanced uses
    - most programs are better served by the use of the weakref collections and
      finalize
    - consider using WeakKeyDictionary, WeakValueDictionary, WeakSet, and finalize
      (which use weak references internally) instead
- WeakValueDictionary implements a mutable mapping where the values are weak references
  to objects
    - corresponding key is automatically removed from WeakValueDictionary when a referred
      object is garbage collected elsewhere in the program
    - commonly used for caching
- a temporary variable may cause an object to last longer than expected by holding a
  reference to it
    - usually not a problem with local variables which are destroyed when the function returns
    - be careful with global variables and loops
- WeakKeyDictionary - keys are weak references
    - can be used to associate additional data with an object owned by other parts of
      an application without adding attributes to those objects
    - can be useful with objects that override attribute accesses
- WeakSet - set class that keeps weak references to its elements
    - element will be discarded when no strong reference to it exist any more
    - can be used to build a class that is aware of every one of its instances
        - class attribute with a WeakSet to hold the references to the instances
        - instances would never be garbage collected with a regular set
- not every Python object may be the target, or referent, of a weak reference
    - basic list and dict instances may not be referents
        - plain subclass of either can be
    - a set instance can be a referent
    - user-defined types can be referents
    - int and tuple instances cannot be targets of weak references, even if subclasses of those types are
      created
    - limitations are implementation details of CPython

 ## Tricks Python Plays with Immutables
- for a tuple t, `t[:]` does not make a copy
    - it returns a reference to the same object
    - `tuple(t)` also returns a reference to the same object
- same behavior can be observed with instances of str, bytes, and frozenset
    - (frozenset is not a sequence, so `fs[:]` does not work if fs is a frozenset)
    - fs.copy() has the same behavior
- sharing of string literals is an optimization technique called interning
    - CPython uses the same technique with small integers to avoid unnecessary
      duplication of common numbers like 0, –1, and 42
    - CPython does not intern all strings or integers
- NOTE: never depend on str or int interning!!!
    - always use == and not is to compare them for equality
- these tricks save memory and make the interpreter faster and are not important for
  everyday programming

# Ch. 9 - A Pythonic Object
- user-defined types can behave like built-in types without inheritance
    - duck typing - just implement the methods needed

## Object Representations
- two methods for getting string representation
    - repr() - developer's view of an object (uses `__repr__`)
    - str() - user's view of an object (uses `__str__`)
- two additional methods for representations
    - `__bytes__` - analogous to str but used to the object represented as
      a byte string
    -  `__format__` - called by `format()` and `str.format()` to get string displays
       of objects using special formatting codes

## Vector Class Redux
- see code for details

## An Alternative Constructor
- when supporting encoding as bytes, need to have ability to decode as bytes
    - see `array.array.frombytes`
    - good to use as a classmethod

## classmethod Versus staticmethod
- use classmethod to define a method that operates on the class and not on instances
    - changes the way the method is called
    - receives the class as the first argument instead of an instance
    - most commonly used for alternative constructors
        - often returns an instance using the class argument
    - first parameter of a class method should be named cls (by convention)
- staticmethod decorator changes a method so that it receives no special first argument
    - like a plain function that happens to live in a class body
- compelling use cases for staticmethod are hard to find
    - just define functions in the module

## Formatted Displays
- `format()` built-in and `str.format()` delegate formatting to each type by
  calling `.__format__(format_spec)`
    - format_spec is a formatting specifier, either
        - second argument in format(my_obj, format_spec)
        - whatever appears after the colon in a replacement field delimited
          with {} inside a format string used with str.format()
- see Format Spec Mini-Languages and Format String Syntax in docs

## A Hashable Vector2d
- implement `__hash__` and `__eq__`
    - if an object implements a custom `__eq__` that takes into account its internal state
      it may be hashable only if all its attributes are immutable
    - must make vector instances immutable
- restricting access to attributes
    - NOTE: use two leading underscores to make an attribute private!!!
    - define a function with the attribute name (without underscores) and decorate
      with the @property decorator - the most simple case is just returning the
      private attribute
    - for methods that only read the value, prefer accessing the public property
```
self.__x = val

@property
def x(self):
    return self.__x
```
- define `__hash__` (can use the hash built-in and XOR the components of an object)
    - see docs for `__hash__` special method

## Private and "Protected" Attributes in Python
- no real protection, just a method for preventing overwriting of "private" attributes
  in a subclass
- name an instance attribute with two leading underscores and zero or at most one trailing underscore
    - Python stores the name in the instance `__dict__` prefixed with a leading underscore
      and the class name (`_Class__attribute`)
    - name mangling in Python
        - designed to prevent accidental access, but not designed for security
- some prefer to use just one underscore prefix to "protect" attributes (e.g. `self._x`)
    - some believe accidental attribute clobbering should be addressed by naming
      conventions rather than double-underscore mangling
    - some explicitly state not to use double-underscore for private
- single underscore prefix has no special meaning to the Python interpreter when
  used in attribute names
    - strong convention among Python programmers that these attributes should not be accessed
      outside of the class
- single underscore prefixed attributes are sometimes called "protected", some call them
  "private"
- NOTE: no way to really make attributes private and immutable

## Saving Space with the `__slots__` Class Attribute
- Python stores instance attributes in a per-instance dict named `__dict__`
    - dictionaries have a significant memory overhead
- `__slots__` class attribute can save a lot of memory by letting the interpreter store the
  instance attributes in a tuple instead of a dict
    - good for dealing with many objects
- NOTE: `__slots__` attribute inherited from a superclass has no effect
    - Python only takes into account `__slots__` attributes defined in each class
- create a class attribute with the name `__slots__` and assign it an iterable of str with
  identifiers for the instance attributes
    - good to use a tuple because it conveys the message that the `__slots__` definition
      cannot change
      By defining __slots__ in the class, you are telling the interpreter: "
- effectively tells interpreter that the specified attributes are all the instance attributes in
  this class
- stored in a tuple-like structure in each instance
    - avoids the memory overhead of the per-instance `__dict__`
- NOTE: use numpy arrays if handling millions of objects with numeric data
    - memory-efficient
    - have highly optimized functions for numeric processing
- class instances will not be allowed to have any other attributes when __slots__ is specified in a class
    - considered bad form to use __slots__ just to prevent users of your class from creating
      new attributes in the instances if they want to
    - use for optimization, not for programmer restraint
    - can add the `__dict__` name to the `__slots__`
        - instances will keep attributes named in __slots__ in the per-instance tuple
        - will also support dynamically created attributes which will be stored in the
          usual `__dict__`
        - may defeat the purpose
- the `__weakref__` attribute is necessary for an object to support weak references
    - present by default in instances of user-defined classes
    - need to include `__weakref__` in `__slots__` if instances may need to be targets
      of weak references
- problems with `__slots__`
    - must remember to redeclare `__slots__` in each subclass
        - inherited attributes are ignored by the interpreter
    - instances will only be able to have the attributes listed in `__slots__`
        - unless including `__dict__` in `__slots__` which may negate the memory savings
    - instances cannot be targets of weak references unless `__weakref__` is included in `__slots__`
- mainly beneficial and to be considered for working with a large number of instances

## Overriding Class Attributes
- class attributes can be used as default values for instance attributes
- writing to an instance attribute that does not exist creates a new instance attribute
    - a class attribute by the same name is untouched
    - any code reading that attribute from that point on will read the instance attribute
      rather than the class attribute (shadowing the class attribute name)
    - allows for customizing individual instances by overriding a class attribute
- to change a class attribute, set it on the class specifically rather than through
  an instance
- common practice to inherit from a class just to override a class attribute (see Django)
- can read the class name rather than hardcoding with
```
class_name = type(self).__name__
```

# Ch. 10 - Sequence Hacking, Hashing, and Slicing

## Vector: A User-Defined Sequence Type
- using composition over inheritance
    - provide access to an array of floats to create an immutable flat
      sequence type

## Vector Take #1: Vector2d Compatible
- NOTE: for > 6 items, repr abbreviates with '...'
    - use reprlib for more
- repr should never raise an exception because of it's use in debugging
- see section for code

## Protocols and Duck Typing
- a protocol is an informal interface defined only in documentation and not in code (in Python)
    - whether a class is a subclass of the protocol class is irrelevant - all that matters is that it
      provides the necessary methods
- duck typing (after Alex Martelli's post)
    - behaving like a sequence is all that matters, i.e. implementing the sequence protocol
    - does not need to be declared anywhere in the code
- can often get away with implementing just part of a protocol (informal and unenforced)
    - to support iteration, only `__getitem__` is required

## Vector Take #2: A Sliceable Sequence
- see code for details
- delegates `__len__` and `__getitem__` to the `self._components` array
    - need to modify `__getitem__` to ensure slices produce Vector items rather
      than array items
- must know slice object to better understand `__getitem__`
    - attributes start, stop, and step, and an indices method
    - `S.indices(len) -> (start, stop, stride)`
        - assuming a sequence of length len, calculate the start and stop indices, and the stride length of
          the extended slice described by S
        - out of bounds indices are clipped in a manner consistent with the handling of
          normal slices
    - indices basically normalizes the values passed to slices
- improved `__getitem__` body
```
cls = type(self)
if isinstance(index, slice):
    return cls(self._components[ index])
elif isinstance(index, numbers.Integral):
    return self._components[index]
else:
    msg = '{cls.__name__} indices must be integers'
    raise TypeError(msg.format(cls=cls))
```
- NOTE: excessive use of isinstance may be a sign of bad OO design
    - using ABCs in isinstance tests makes an API more flexible and future-proof
    - no ABC for slice in the Python 3.4 standard library

## Vector Take #3: Dynamic Attribute Access
- the `__getattr__` method is invoked by the interpreter when attribute lookup fails
    - a call to `obj.x` causes the interpreter to check if the instance has an attribute named x
    - if not, the search goes to the `class(obj.__class__)`
    - then it goes up the inheritance graph
    - if the x attribute is not found, then the `__getattr__` method defined in the class
      of obj is called with self and the name of the attribute as a string (e.g., 'x')
- NOTE: `__getattr__` is only called as a fallback
    - if an attribute is dynamically assigned to an instance, the lookup described above
      will not occur
- can use `__setattr__` to prevent assignment of undesired names, or generally control
  assignment to attributes
- NOTE: the `super()` function a way to access methods of superclasses dynamically
    - necessary in a dynamic language that supports multiple inheritance
    - used to delegate some task from a method in a subclass to a method in a superclass
- common to need to implement `__setattr__` when `__getattr__` is required

## Vector Take #4: Hashing and a Faster ==
- using functools.reduce to apply hash and XOR to each component of the array
    - can be replaced by sum in many cases
```
hashes = (hash(x) for x in self._components)
return functools.reduce(operator.xor, hashes, 0)
```
- good practice to provide the third argument, reduce(function, iterable, initializer),
  to prevent a TypeError on an empty sequence with no initial value
  - initializer is the value returned if the sequence is empty and is used as the
    first argument in the reducing loop
  - should be the identity value of the operation
  - for +, |, ^ the initializer should be 0
  - for *, & it should be 1
- perfect example of map/reduce
```
hashes = map(hash, self._components)
return functools.reduce(operator.xor, hashes)
```
- NOTE: map is less efficient in Python 2 because it builds a new list
    - Python 3 builds a lazy generator
- can also replace `__eq__`
```
return len(self) == len(other) and all(a == b for a, b in zip(self, other))
```

## Vector Take #5: Formatting
- NOTE: when extending the Format Specification Mini-Language it's best to avoid
  reusing format codes supported by built-in types
- see code for support of hyperspherical coordinates


# Ch. 11 - Interfaces: From Protocols to ABCs
## Interfaces and Protocols in Python Culture
- every class has an interface
    - the set public attributes (methods or data attributes) implemented or inherited by
      the class
    - includes special methods, like `__getitem__` or `__add__`
    - protected and private attributes are not part of an interface by definition
        - even if "protected" is merely a naming convention (the single leading underscore)
        - even if private attributes are easily accessed
    - possible and even OK to have public data attributes as part of the interface of an object
        - if necessary a data attribute can be turned into a property implementing getter/setter
          logic without breaking client code
- complementary definition of interface
    - the subset of an object's public methods that enable it to play a specific role
      in the system
- interface as a set of methods to fulfill a role is what == procotol in Smalltalk
- protocols are independent of inheritance
     - a class may implement several protocols
     - enables instances to fulfill several roles
- protocols are informal interfaces and are therefore not enforced like formal interfaces
    - may be partially implemented in a particular class

## Python Digs Sequences
- see code

## Monkey-Patching to Implement a Protocol at Runtime
- following established protocols improves chances of working with existing library
  functions etc
- monkey-patching - changing code at runtime
- shows example of adding methods to a class at runtime to allow it to work with
  random.shuffle
- alsow highlights that protocols are dynamic

## Alex Martelli's Waterfowl
- Alex Martelli - Waterfowl and ABCs
    - duck typing
        - ignoring an object's actual type
        - ensure that the object implements the method names, signatures, and semantics required
          for its intended use
    - mostly boils down to avoiding the use of isinstance to check the object's type in Python
        - also worse approach of checking whether `type(foo) is bar`
        - inhibits even the simplest forms of inheritance
    - alternate approach to duck typing has evolved over time
    - the presence of two identical methods on a type doesn't imply interchangeability
        - programmer needs to be able to assert something to be assured of interchangeability
    - duck typing -> goose typing
    - major advantage of ABCs
        - the register class method lets end-user code "declare" that a certain class becomes
          a "virtual" subclass of an ABC
        - registered class must meet the ABC's method name and signature requirements
        - more importantly must meet the underlying semantic contract of the signature requirements
        - does not have to have been developed with any awareness of the ABC (i.e. inherit from it)
        - breaks rigidity and strong coupling that make inheritance something to use with
          much more caution than typically practiced by most OOP
    - in some cases, classes do not even need to be registered as a subclass to be recognized
    - use `isinstance(instance, ABC)`
    - don't define custom ABCs in production code

- inheriting from an ABC is more than implementing the required methods
    - also a declaration of intent by the developer
    - intent can also be made explicit through registering a virtual subclass
- use of isinstance and issubclass becomes more acceptable to test against ABCs
    - these functions previously worked against duck typing
    - if a component does not implement an ABC by subclassing, it can be registered after
      the fact so it passes those explicit type checks
      . However, even with ABCs, you should beware that
- excessive use of isinstance checks may be a code smell even with ABCs
    - should use polymorphism for that
    - design classes so that the interpreter dispatches calls to the proper methods
    - exceptions
        - some Python APIs accept a single str or a sequence of str items
            - wrap the str in a list to ease processing
        Because str is a sequence type,
        - the simplest way to distinguish the string from any other immutable sequence is to
          do an explicit `isinstance(x, str)` (since it is a sequence type)
    - usually OK to perform an insinstance check against an ABC if an API contract must be enforces
- duck typing is often simpler and more flexible than type checks outside of frameworks
- do not overuse ABCs
    - ABCs are meant to encapsulate very general concepts, abstractions, introduced by a 
      framework 
    - most programmers most likely don't need to write any new ABCs
        - use existing ones correctly

## Subclassing an ABC
- interpreter does not check for the implementation of the abstract methods at import time
    - only checked at runtime when an implementing instance is instantiated
    - if any abstract method is not implemented a TypeError exception is thrown
- concrete methods in each collections.abc ABC are implemented in terms of the public
  interface of the class
    - work without any knowledge of the internal structure of instances
- NOTE: when coding a concrete subclass it may be possible to override methods inherited from
  ABCs with more efficient implementations
    - `__contains__` works by doing a full scan of the sequence
        - can speed a sorted concrete sequence using binary search with bisect
## ABCs in the Standard Library
- ABCs have been available since 2.6
    - most in collections.abc module
    - numbers and io packages
- two modules named abc in the standard library
    - in Python 3.4 implemented outside of the collections package to reduce load time
    - other abc module is just abc (i.e., Lib/ abc.py)
        - defines abc.ABC class
        - depended on by every ABC but not needed to extend ABCs
- see figure 11-3 or the table in the docs for inheritance structure in ABC
- much multiple inheritance, but mostly for mixin methods
- Iterable, Container, and Sized
    - every collection should either inherit from these ABCs or at least implement compatible protocols
    - Iterable supports iteration with `__iter__`
    - Container supports the in operator with `__contains__`
    - Sized supports `len()` with `__len__`
- Sequence, Mapping, and Set
    - main immutable collection types
    - each has a mutable subclass
- MappingView
    - objects returned from the mapping methods `.items()`, `.keys()`, and `.values()` inherit
      from ItemsView, ValuesView, and ValuesView in Python 3
    - first two also inherit the interface of Set
- Callable and Hashable
    - not limited to collections
    - collections.abc was the first package to define ABCs in the standard library
    - rare to see subclasses of either Callable or Hashable
    - main use is to support the `insinstance` built-in as a safe way of
      determining whether an object is callable or hashable
- Iterator
    - iterator subclasses Iterable

- numbers packages
    - Number
    - Complex
    - Real
    - Rational
    - Float
    - can use each with `isinstance`
    - decimal.Decimal is not registered as a virtual subclass of numbers.Real
        - need protection from accidental mixing of decimals with other less precise
          numeric types (i.e. floats) when the precision of Decimal is require

## Defining and Using an ABC
- common to use in frameworks
- example class is designed designed to pick items at random from a finite set,
  without repeating, until the set is exhausted
- abstract methods
    - `.load():` - put items into the container
    - `.pick():` - remove one item at random from the container, returning it
- concrete methods
    - `.loaded():` - return True if there is at least one item in the container
    - `.inspect():` -  return a sorted tuple built from the items currently in the
      container, without changing its contents (its internal ordering is not preserved)
- mark an abstract method with @abc.abstractmethod and generally leave the body empty
  (but add a docstring)
- NOTE: an abstract method can have an implementation
    - subclasses will still be forced to override it
    - will be able to invoke the abstract method with super()
        - adds functionality to it instead of implementing from scratch
    - see the abc module documentation for details on @abstractmethod usage

- to declare an ABC, subclass abc.ABC or any other ABC
    - abc.ABC class is new in Python 3.4
    - for earlier versions of Python use the metaclass=keyword in the class statement
      and point to abc.ABCMeta `class Tombola(metaclass=abc.ABCMeta):`
- metaclass=keyword argument was introduced in Python 3
- use the __metaclass__ class attribute in Python 2
```
class Tombola(object):
    __metaclass__ = abc.ABCMeta
```
- a metaclass is a special kind of class explained later
- abc module defines the following decorators
    - @abstractmethod
    - @abstractclassmethod
    - @abstractstaticmethod
    - @abstractproperty
    - last three are deprecated since Python 3.3
        - possible to stack decorators on top of @abstractmethod
        - the preferred way to declare an abstract class method is
```
@classmethod
@abc.abstractmethod
def an_abstract_classmethod(cls, ...):
    pass
```
- NOTE: order of stacked function decorators matters, check docs
- see code for details of subclassing the custom ABC

- with goose typing programmer can register a class as a virtual subclass of an
  ABC even if it does not inherit from it
    - declares contract that the class faithfully implements the interface defined
      in the ABC
    - interpreter assumes this is true without checking
    - if not implemented, runtime exceptions will occur
- done by calling a register method on the ABC
    - registered class then becomes a virtual subclass of the ABC
        - will be recognized by functions like issubclass and isinstance
        - will not inherit any methods or attributes from the ABC
- NOTE: virtual subclasses do not inherit from their registered ABCs
    - they are not checked for conformance to the ABC interface at any time
    - up to the subclass to actually implement all the methods needed to avoid
      runtime errors
- register method is usually invoked as a plain function
    - can also used as a decorator `@ABCClass.register`
- `issubclass` and `isinstance` work but `__mro__` does not show the ABC as a class
  that is inherited from

## How the Tombola Subclasses Were Tested
- class attributes that allow introspection of a class hierarchy
    - `__subclasses__()`
        - method that returns a list of the immediate subclasses of the class
            - does not include virtual subclasses
    - `_abc_registry`
        - data attribute that is only available in ABCs
        - bound to a WeakSet with weak references to registered virtual subclasses
          of the abstract class
- see code for details

## Usage of register in Practice
- `register` had to be called as plain function prior to Python 3.3
    - often still called as a function e.g. `Sequence.register(tuple)`

## Geese Can Behave as Ducks
- class can be recognized as a virtual subclass of an ABC even without registration
Struggle is considered a subclass of abc.Sized by the
- issubclass and isinstance functions consider example class to be a subclass of the ABC
  because the ABC implements a special class method named `__subclasshook__`
    - for Sized, checks if `__len__` is in the `__dict__` for all of the inherited classes
      in `__mro__` of the class being checked
- probably not a good idea to implement `__subclasshook__` in your own ABCs


# Ch. 12 - Inheritance: For Good or For Worse
## Subclassing Built-In Types Is Tricky
- not possible to subclass built-in types before Python 2.2.
- can now be done
    - code of the built-ins, written in C, does not call special methods overridden
      by user-defined classes
    - CPython has no rule for when overridden method of subclasses of built-in types
      get implicitly called or not
        - methods are never called by other built-in methods of the same object
- violates basic rule of OO
    - method search should always start from base class of target instance and
      propagate upwards
- problem applies only to method delegation within the C language implementation of the
  built-in types
    - only affects user-defined classes derived directly from those types
    - subclassing from a class coded in Python will not cause the same problems

## Multiple Inheritance and Method Resolution Order
- consider diamond inheritance classes in multiple inheritance
- can explicitly call superclass methods
```
i.method()
P.method(i)
```
- Python follows a specific order when traversing the inheritance graph
    - called MRO - Method Resolution Order
    - classes have an attribute called `__mro__` holding a tuple of references to the
      superclasses in MRO order
        - current class all the way to the object class
- recommended way to delegate method calls to superclasses is the super() built-in function
    - easier to use in Python 3
- possible to bypass the MRO and invoke a method on a superclass directly
    - must pass self explicitly when calling an instance method directly on a class
        - accessing an unbound method
- safest and most future-proof to call super
    - especially when using frameworks and class hierarchies that are externally
      controlled
- MRO is computed using an algorithm called C3
    - "The Python 2.3 Method Resolution Order" is the canonical paper
    - not necessary to understand unless making strong use of multiple inheritance

## Multiple Inheritance in the Real World
- Adapter pattern uses MI
- collections.abc uses MI but more for interfaces
- Tkinter GUI toolkit uses extensively due to widget-based approach
- see book for descriptions of classes and hierarchy

## Coping with Multiple Inheritance
1. distinguish interface inheritance from implementation inheritance
    - useful to keep the reasons why subclassing is done straight when using MI
        - inheritance of interface creates a subtype, implying an "is-a" relationship
        - inheritance of implementation avoids code duplication by reuse
     - both uses are often simultaneous
        - make intent clear when possible
     - code reuse is an implementation detail and can often be replaced by composition and delegation
     - interface inheritance is the backbone of a framework
2. make interfaces explicit with abcs
    - if a class is designed to define an interface it should be an explicit ABC in modern Python
        - subclass abc.ABC or another ABC
3. use mixins for code reuse
    - a class is designed to provide method implementations for reuse by multiple
      unrelated subclasses should be an explicit mixin class if it doesn't imply an "is-a" relationship
    - a mixin does not define a new type (conceptually)
        - bundles methods for reuse
    - never instantiate
    - concrete classes should not only inherit from a mixin
    - a single mixin should provide a single specific behavior with a few closely related methods
4. make mixins explicit by naming
    - no formal method to define a mixin
        - highly recommended to name with a Mixin suffix
5. an abc may also be a mixin; the reverse is not true
    - when an ABC can implement concrete methods it can work as a mixin also
    - an ABC defines a type
        - mixin does not
    - an ABC can be the sole base class of any other class
        - mixin should not be (except in special cases)
    - a restriction applied to ABCs and not mixins
        - concrete methods implemented in an ABC should only collaborate with methods
          of the same ABC and its superclasses
6. don’t subclass from more than one concrete class concrete
    - classes should have zero or at most one concrete superclass
    - all but one of the superclasses of a concrete class should be ABCs or mixins
7. provide aggregate classes to users
    - provide a class that brings classes together in a sensible way if some
      combination of ABCs or mixins is useful to client code
    - aggregate class
8. favor object composition over class inheritance
    - best advice
    - easy to overuse inheritance
    - hierarchy appeals to a sense of order
    - favoring composition leads to more flexible designs
    - in many cases, a class that

- see text for issues with Tkinter

## A Modern Example: Mixins in Django Generic Views
- see text for description


# Ch. 13 - Operator Overloading: Doing It Right
## Operator Overloading 101
- can be abused, but can also lead to clean programming constructs
- limitations in Python
    - cannot overload operators for the built-in types
    - cannot create new operators, only overload existing ones
    - some operators can’t be overloaded - is, and, or, not (but the bitwise &, |, ~, can)

## Unary Operators
- `-(__neg__)`
    - arithmetic unary negation
- `+(__pos__)`
    - arithmetic unary plus
    - usually x == +x
    - cases not true
- `~(__invert__)`
    - bitwise inverse of an integer, defined as ~x == -(x + 1)
- abs() built-in function is listed as a unary operator
    - `__abs__` special method
- to support, implement the appropriate special method, which will receive just one
  argument - self
    - use logic that makes sense for the class
    - fundamental rule of operators - always return a new object
        - do not modify self
        - create and return a new instance of a suitable type
- cases where x and +x are not equal

 Everybody expects that x = = + x, and that is true almost all the time in Python,
 but I found two cases in the standard library where x != + x. The
 first case involves the
    - decimal.Decimal class
        - can have x != +x if x is a Decimal instance created in an arithmetic context and +x
          is then evaluated in a context with different settings
    - collections.Counter documentation
        - Counter class implements infix + to add the tallies from two Counter instances
        - Counter addition discards from the result any item with a negative or zero count
        - prefix + is a shortcut for adding an empty Counter
            - produces a new Counter preserving only the tallies that are greater than zero

## Overloading + for Vector Addition
- for Sequence types, + and * should be used for concatenation and repetition
- see book for code examples
- NOTE: only augmented assignment operators should modify self
    - all others should return a new object
- NOTE: do not confuse NotImplemented with NotImplementedError
    - NotImplemented is a special singleton value that an infix operator special method
      should return to tell the interpreter it cannot handle a given operand
    - NotImplementedError is an exception that stub methods in abstract classes raise
      to warn that they must be overwritten by subclasses
- NOTE: an infix operator that raises an exception aborts the operator dispatch algorithm
    - for TypeError it is often better to catch it and return NotImplemented
    - allows the interpreter to try calling the reversed operator method

## Overloading * for Scalar Multiplication
- see book for code examples
- see table 13-1 for infix operator method names
- Python 3.5 adds a dedicated infix matrix multiplication operator

## Rich Comparison Operators
- operators ==, !=, >, <, >=, <= differ in two aspects
    - same set of methods are used in forward and reverse operator calls
    - both the forward and reverse calls invoke `__eq__` for ==, only swapping arguments
    - a forward call to `__gt__` is followed by a reverse call to `__lt__` with the
      swapped arguments
    - for == and !=, if the reverse call fails, Python compares the object IDs instead of
      raising TypeError
- see table 13-2 for rich comparison operators
- NOTE: the fallback step for all comparison operators changed from Python 2
    - Python 3 returns the negated result of `__eq__` for `__ne__`
    - Python 3 raises TypeError for the ordering comparison operators
    - in Python 2 ordering comparisons produced weird results taking into account object
      types and IDs in some arbitrary way
      - raising TypeError is an improvement because it makes more sense for comparing
        something like a tuple and an int
- Python 3 documentation bug
    - the rich comparison method documentation states that the truth of x == y does
      not imply that x! = y is false
    - when defining `__eq__()`, one should also define `__ne__()` so that the operators
      will behave as expected
        - true for Python 2
        - not good in Python 3
            - a useful default `__ne__` implementation is inherited from the object class
            - rarely necessary to override it

## Augmented Assignment Operators
- if the in-place operators are not implemented by a class the augmented assignment operators
  are just syntactic sugar
    - a += b is evaluated exactly as a = a + b
    - expected behavior for immutable types
    - if `__add__` is implemented then += will work with no additional code
    - if an in-place operator method such as `__iadd__` is implemented, that method is
      called to compute the result of a += b
- expected to change the lefthand operand in place
    - not create a new object as the result
- NOTE: in-place special methods should never be implemented for immutable types
- NOTE: can observe contrasting behavior of + and += by looking at how the list built-in
  works
    - can only concatenate one list to another list with infix +
    - can extend the lefthand list with items from any iterable on the righthand side of operator +=
        - consistent with how the list.extend() method works
- `__add__`
    - result is produced by calling the constructor to build a new instance
- `__iadd__`
    - result is produced by returning self after it has been modified
    . To wrap up this example, a final observation on Example   13-18: by design,
- `__radd__` may not be necessary
    - the forward method `__add__` will only deal with righthand operands of the same type
- NOTE: if a forward infix operator method (e.g., `__mul__`) is designed to
  work only with operands of the same type as self it is useless to implement the
  corresponding reverse method (e.g., `__rmul__`)
    - by definition reverse method will only be invoked when dealing with an operand
      of a different type


# Ch. 14 - Iterables, Iterators, and Generators


