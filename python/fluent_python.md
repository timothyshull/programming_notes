# Python Docs
## Built-in Types
### Truth Value Testing
- any object in if or loop conditional
- falsey types:
    - None
    - False
    - 0, 0L, 0.0, 0j
    - empty sequence `'', (), []`
    - empty mapping {}
    - user-defined class with `__nonzero__` or `__len__` that return integer 0 or false
### Boolean Operations - and, or, not (in ascending priority)
- or
- and
- not
### Comparisons
- <, <=, >, >=, ==, !=, is, is not
- can override `__lt__` etc, but can also override `__cmp__`
### Numeric Types - int, float, long, complex
- booleans are a subtype of plain integers
- ints are always C longs (sys.maxint)
- floats are usually doubles (sys.float_info)
- complex - z.imag, z.real
- fractions - hold rationals
- decimal - floats with user-defined precision
- can use ctors float(), int(), long(), complex() etc
- general numeric operators with
    - .conjugate()
    - divmod()
    - pow() or `**`
- all numbers.Real types (int, long, and float) also include
    - math.trunc(x)
    - round(x, n)
    - math.floor(x)
    - math.ceil(x)
### Bitwise Operations on Integer Types
### Additional Methods on Integer Types
- int.bit_length()
- long.bit_length()
### Additional Methods on Float Types
- float.as_integer_ratio() - returns pair
- float.is_integer()
- float.hex()
- float.fromhex()
### Iterator Types
- `container.__iter__()`
- `iterator.__iter__()` - return self
- `iterator.next()` - raises StopIteration on end
### Sequence Types - str, unicode, list, tuple, bytearray, buffer, xrange
- string literals - 'fdsa' or "fksdla"
- unicode strings - u'sjkdf' or u"dfsjakl"
- lists - `[0, 1, 2]`
- tuple - elem1, elem2, elem3 or (elem1, elem2, elem3) always () for an empty tuple and (elem1,) for a single item
- bytearray - built-in bytearray() function - mutable sequence of integers from [0, 256)
- buffer - not directly supported by Python syntax
- xrange - similar to buffer
- supports
    - x in s
    - x not in s
    - s + t - concatenation
    - s * n - add s to itself n times
    - `s[i]` - 0 indexed ith item
    - `s[i:j]` - slice
    - `s[i:j:k]` - slice with step
    - len(s)
    - min(s)
    - max(s)
    - s.index(x) - index of first occurrence
    - s.count(x) - number of x in s
### String Methods
- .capitalize()
- .center(width, fill character)
- .count(substring, start, end) - num of non-overlapping occurrences
- .decode(encoding, errors)
- .encode(encoding, errors)
- .endswith(suffix, start, end)
- .expandtabs(tabsize)
- .find(sub, start, end)
- .format() - new standard for string formatting
- .index(sub, start, end) - like find but raises ValueError
- .isalnum()
- .isalpha()
- .isdigit()
- .islower()
- .isspace()
- .istitle()
- .isupper()
- .join(iterable) - concatenate elements of iterable with src string as separator
- .ljust(width, fill character)
- .lower()
- `.lstrip([chars])`
- .partition(sep)
- .replace(old, new, count)
- .rfind(sub, start, end)
- .rindex(sub, start, end)
- .rjust(width, fill character)
- .rpartition(sep)
- .rsplit(sep, maxsplit)
- .rstrip([chars])
- .split(sep, maxsplit)
- .splitlines(sep, maxsplit)
- `.splitlines([keepends])` - keeps
- .startswith(prefix, start, end)
- .strip([chars])
- .swapcase()
- .title() - title cased
- .translate(table, delete characters
- .upper()
- .zfill(width) - filled with 0's
- unicode.isnumeric()
- unicode.isdecimal()

### String Formatting Operations
### XRange Type
### Mutable Sequence Types
### Set Types - set, frozenset
### Mapping Types - dict
### File Objects
### memoryview type
### Context Manager Types
### Other Built-in Types
- Modules
- Classes and Class instances
- Functions
- Methods
- Code Objects
- Type Objects
- The Null Object
- The Ellipsis Object
- The NotImplemented Object
- Boolean values
    - bool()
- Internal Objects
### Special Attributes
- `__dict__`
- `__methods__`
- `__members__`
- `__class__`
- `__bases__`
- `__name__`
- attributes for new-style classes (inherits from object, required for `__slots__` and `__getattribute__`)
    - `__mro__`
    - .mro()
    - `__subclasses__`

# Note review the built-in functions, specifically chr


# Effective Python
## 1. Pythonic Thinking
3. Know the difference between bytes, str, and unicode
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


# The Python Data Model
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