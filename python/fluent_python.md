# Chapter 1: Pythonic Thinking
## know which version of python you’re using
## follow the pep 8 style guide
## know the differences between bytes, str, and unicode
- P3:
    - bytes -> 8-bit values
    - str -> Unicode characters
    - bytes and str cannot interoperate
- P2:
    - str -> 8-bit values
    - unicode -> Unicode characters
    - can interoperate if str only contains 7-bit ASCII characters
- use helper functions to ensure input is of expected type
- use 'rb' and 'wb' for binary data
## write helper functions instead of complex expressions
## know how to slice sequences
## avoid using start, end, and stride in a single slice
## use list comprehensions instead of map and filter
## avoid more than two expressions in list comprehensions
## consider generator expressions for large comprehensions
## prefer enumerate over range
## use zip to process iterators in parallel
## avoid else blocks after for and while loops
## take advantage of each block in try/except/else/finally

# Chapter 2: Functions
## prefer exceptions to returning none
## know how closures interact with variable scope
## consider generators instead of returning lists
## be defensive when iterating over arguments
## reduce visual noise with variable positional arguments
## provide optional behavior with keyword arguments
## use none and docstrings to specify dynamic default arguments
## enforce clarity with keyword-only arguments

# Chapter 3: Classes and Inheritance
## prefer helper classes over bookkeeping with dictionaries and tuples
## accept functions for simple interfaces instead of classes
## use @classmethod polymorphism to construct objects generically
## initialize parent classes with super
## use multiple inheritance only for mix-in utility classes
## prefer public attributes over private ones
## inherit from collections.abc for custom container types

# Chapter 4: Metaclasses and Attributes
## use plain attributes instead of get and set methods
## consider @property instead of refactoring attributes
## use descriptors for reusable @property methods
## use __getattr__, __getattribute__, and __setattr__ for lazy attributes
## validate subclasses with metaclasses
## register class existence with metaclasses
## annotate class attributes with metaclasses

# Chapter 5: Concurrency and Parallelism
## use subprocess to manage child processes
## use threads for blocking i/o, avoid for parallelism
## use lock to prevent data races in threads
## use queue to coordinate work between threads
## consider coroutines to run many functions concurrently
## consider concurrent.futures for true parallelism

# Chapter 6: Built-in Modules
## define function decorators with functools.wraps
## consider contextlib and with statements for reusable try/finally behavior
## make pickle reliable with copyreg
## use datetime instead of time for local clocks
## use built-in algorithms and data structures
## use decimal when precision is paramount
## know where to find community-built modules

# Chapter 7: Collaboration
## write docstrings for every function, class, and module
## use packages to organize modules and provide stable apis
## define a root exception to insulate callers from apis
## know how to break circular dependencies
## use virtual environments for isolated and reproducible dependencies

# Chapter 8: Production
## consider module-scoped code to configure deployment environments
## use repr strings for debugging output
## test everything with unittest
## consider interactive debugging with pdb
## profile before optimizing
## use tracemalloc to understand memory usage and leaks
