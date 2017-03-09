# Effective C++
## Accustoming Yourself to C++
1. View C++ as a federation of languages
2. Prefer consts, enums, and inlines to #defines
3. Use const whenever possible
4. Make sure that objects are initialized before they're used

## Constructors, Destructors, and Assignment Operators
5. Know what functions C++ silently writes and calls
6. Explicitly disallow use of compiler-generated functions you do not want
7. Declare destructors virtual in polymorphic base classes
8. Prevent exceptions from leaving destructors
9. Never call virtual functions during construction or destruction
10. Have assignment operators return a reference to *this
11. Handle assignment to self in operator=
12. Copy all parts of an object

## Resource Management
13. Use objects to manage resources (RAII)
14. Think carefully about copying behavior in resource managing classes
15. Provide access to raw resources in resource-managing classes
16. Use the same form in corresponding uses of new and delete
17. Store newed objects in smart pointers in standalone statements

## Designs and Declarations
18. Make interfaces easy to use correctly and hard to use incorrectly
19. Treat class design as type design
20. Prefer pass-by-reference-to-const to pass-by-value
21. Don't try to return a reference when you must return an object
22. Declare data members private
23. Prefer non-member non-friend functions to member functions
24. Declare non-member functions when type conversions should
    apply to all parameters
25. Consider support for a non-throwing swap

## Implementations
26. Postpone variable definitions as long as possible
27. Minimize casting
28. Avoid returning "handles" to object internals
29. Strive for exception-safe code
30. Understand the ins and outs of inlining
31. Minimize compilation dependencies between files

## Inheritance and Object-Oriented Design
32. Make sure public inheritance models "is-a"
33. Avoid hiding inherited names
34. Differentiate between inheritance of interface and inheritance of implementation
35. Consider alternatives to virtual functions
36. Never redefine an inherited non-virtual function
37. Never redefine a function's inherited default parameter value
38. Model "has-a" or "is-implemented-in-terms-of" through composition
39. Use private inheritance judiciously
40. Use multiple inheritance judiciously

## Templates and Generic Programming
41. Understand implicit interfaces and compile-time polymorphism
42. Understand the two meanings of typename
43. Know how to access names in templatized base classes
44. Factor parameter-independent code out of templates
45. Use member function templates to accept "all compatible types"
46. Define non-member functions inside templates when type conversions are desired
47. Use traits classes for information about types
48. Be aware of template metaprogramming

## Customizing new and delete
49. Understand the behaviour of the new-handler
50. Understand when it makes sense to replace new and delete
51. Adhere to convention when writing new and delete
52. Write placement delete if you write placement new

## Miscellany
53. Pay attention to compiler warnings
54. Familiarize yourself with the standard library
55. Familiarize yourself with Boost



# More Effective C++
## Basics
1. Distinguish between pointers and references
2. Prefer C++ style casts
3. Never treat arrays polymorphically
4. Avoid gratuitous default constructors

## Operators
5. Be wary of user-defined conversion functions
6. Distinguish between prefix and postfix forms of increment and decrement operators
7. Never overload &&, ||, or ,
8. Understand the different meanings of new and delete

## Exceptions
9. Use destructors to prevent resource leaks
10. Prevent resource leaks in constructors
11. Prevent exceptions from leaving destructors
12. Understand how throwing an exception differs from
    passing a parameter or calling a virtual function
13. Catch exceptions by reference
14. Use exception specifications judiciously
15. Understand the costs of exception handling

## Efficiency
16. Remember the 80-20 (90-10) rule
17. Consider using lazy evaluation
18. Amortize the cost of expected computations
19. Understand the origin of temporary objects
20. Facilitate the return value optimization
21. Overload to avoid implicit type conversions
22. Consider using op= instead of stand-alone op
23. Consider alternative libraries
24. Understand the costs of virtual functions, multiple
    inheritance, virtual base classes, and RTTI

# Techniques
25. Virtualizing constructors and non-member functions
26. Limiting the number of objects of a class
27. Requiring or prohibiting heap-based objects
28. Smart pointers
29. Reference counting
30. Proxy classes
31. Making functions virtual with respect to more than one
    object

# Miscellany
32. Program in the future tense
33. Make non-leaf classes abstract
34. Understand how to combine C++ and C in the same program
35. Familiarize yourself with the language standard



# Effective STL
## Containers
1. Choose your containers with care
2. Beware the illusion of container independent code
3. Make copying cheap and correct for objects in containers
4. Call empty instead of checking size() against zero
5. Prefer range member functions to their single-element counterparts
6. Be alert for C++'s most vexing parse
7. When using containers of newed pointers, remember to delete
   the pointers when the container is destroyed
8. Never create containers of auto_pts
9. Choose carefully among erasing options
10. Be aware of allocator conventions and restrictions
11. Understand the legitimate uses of custom allocators
12. Have realistic expectations about the thread safety of STL
    containers

## vector and string
13. Prefer vector and string to dynamically allocated arrays
14. Use reserve to avoid unnecessary reallocations
15. Be aware of variations in string implementations (some
    removed by C++11)
16. Know how to pass vector and string data to legacy APIs
17. Use "the swap trick" to trim excess capacity (use
    .shrink_to_fit after C++11)
18. Avoid using vector<bool>

## Associative Containers
19. Understand the difference between equality and equivalence
20. Specify comparison types for associative containers
21. Always have comparison functions return false for equal values
22. Avoid in-place key modification in set and multiset
23. Consider replacing associative containers with sorted vectors
24. Choose carefully between `map::operator[]` and
    map::insert when efficiency is important
25. Familiarize yourself with the nonstandard hashed containers

## Iterators
26. Prefer iterator to const_iterator, reverse_iterator,
    and const_reverse_iterator
27. Use distance and advance to convert a container's
    const_iterators to iterators
28. Understand how to use reverse iterator's base iterator
29. Consider istreambuf iterators for character-by-character input

## Algorithms
30. Make sure destination ranges are big enough
31. Know your sorting options
32. Follow remove-like algorithms by erase if you really want
    to remove something
33. Be wary of remove-like algorithms on containers of pointers
34. Note which algorithms expect sorted ranges
35. Implement simple case-insensitive string comparisons via
    mismatch or lexicographical_compare
36. Understand the proper implementation of copy_if
37. Use accumulate or for_each to summarize ranges

## Functors, Functor Classes, Functions, etc
38. Design functor classes for pass-by-value
39. Make predicates pure functions
40. Make functor classes adaptable
41. Understand the reasons for ptr_fun, mem_fun, and
    mem_fun_ref
42. Make sure less<t> means operator<

## Programming with the STL
43. Prefer algorithm calls to hand-written loops
44. Prefer member functions to algorithms with the same names
45. Distinguish among count, find, binary_search, lower_bound,
    upper_bound, and equal_range
46. Consider function objects instead of functions as algorithm
    parameters
47. Avoid producing write-only code
48. Always #include the proper headers
49. Learn to decipher STL-related compiler diagnostics
50. Familiarize yourself with STL-related web sites



# Effective Modern C++
## Deducing Types
1. Understand template type deduction
2. Understand auto type deduction
3. Understand decltype
4. Know how to view deduced types

## auto
5. Prefer auto to explicit type declarations
6. Use the explicitly typed initializer idiom when auto
   deduces undesired types

## Moving to Modern C++
7. Distinguish between () and {} when creating objects
8. Prefer nullptr to 0 and NULL
9. Prefer alias declarations to typedefs
10. Prefer scoped enums to unscoped enums
11. Prefer deleted functions to private undefined ones
12. Declare overriding functions override
13. Prefer const_iterators to iterators
14. Declare functions noexcept if they won't emit exceptions
15. Use constexpr whenever possible
16. Make const member functions thread safe
17. Understand special member function generation

## Smart Pointers
18. Use std::unique_ptr for exclusive-ownership resource
    management
19. Use std::shared_ptr for shared-ownership resource
    management
20. Use std::weak_ptr for std::shared_ptr-like pointers that
    can dangle
21. Prefer std::make_unique and std::make_shared to direct
    use of new
22. When using the Pimpl idiom, define special member
    functions in the implementation file

## Rvalue References, Move Semantics, and Perfect Forwarding
23. Understand std::move and std::forward
24. Distinguish universal references from rvalue references
25. Use std::move on rvalue references, std::forward on
    universal references
26. Avoid overloading on universal references
27. Familiarize yourself with overloading on universal
    references
28. Understand reference collapsing
29. Assume that move operations are not present, are not
    cheap, and not used
30. Familiarize yourself with perfect forwarding failure
    cases

## Lambda Expressions
31. Avoid default capture modes
32. Use init capture to move objects into closures
33. Use decltype on auto&& parameters to std::forward them
34. Prefer lambdas to std::bind

## The Concurrency API
35. Prefer task-based programming to thread-based
36. Specify std::launch::async if asynchronicity is essential
37. Make std::threads unjoinable on all paths
38. Be aware of varying thread handle destructor behavior
39. Consider void futures for one-shot event communication
40. Use std::atomic for concurrency, volatile for special memory

## Tweaks
41. Consider pass-by-value for copyable parameters that are
    cheap to move and always copied
42. Consider emplacement instead of insertion