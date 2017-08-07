# Ch. 1 - Hello, world of concurrency in C++!
- C++11 standardized threading and concurrency in without reliance on external libraries

## 1.1 What is concurrency?
- two or more separate activities happening at the same time

### Concurrency in computer systems
- single system performing multiple independent activities in parallel, rather than sequentially, or one after
  the other
- single core can only perform one task at a time but can switch between many tasks very rapidly
- task switching
- hardware concurrency - multiple processors or multiple cores within a processor (or both), computers that
  are capable of running more than one task in parallel
- in order to interleave task execution the system has to perform a context switch every
  time it changes from one task to another which takes time
- to perform a context switch the OS has to
    - save the CPU state and instruction pointer for the currently running task
    - determine which task to switch to
    - reload the CPU state for the task being switched to
    - potentially load the memory for the instructions and data for the new task into cache
        - can prevent the CPU from executing any instructions causing further delay
- some processors can run more than one thread on a single processor
- the number of hardware threads is the important factor to consider to measure how many independent
  tasks the hardware can genuinely run concurrently

### Approaches to concurrency
- communication is difficult
- two main ways to achieve concurrency in programs
    - multiple processes
    - multiple threads

#### Concurrency with multiple processes
- divide the application into multiple, separate, single-threaded processes that are run at
  the same time
    - separate processes pass messages to each other through all the normal interprocess communication channels
        - signals
        - sockets
        - files
        - pipes
        - etc.
- communication between processes can be
    - complicated to set up
    - slow
- difficult to share data
- more downsides
    - process startup takes time
    - OS must devote internal resources to managing processes
- updsides
    - added protection operating systems typically provide between processes
    - higher-level communication mechanisms mean that it can be easier to write safe concurrent
      code with processes rather than threads
    - can run the separate processes on distinct machines connected over a network

#### Concurrency with multiple threads
- like lightweight processes
    - each thread runs independently of the others
    - each thread may run a different sequence of instructions
    - all threads in a process share the same address space
    - most of the data can be accessed directly from all threads
        - global variables remain global
        - pointers or references to objects or data can be passed around among threads
- possible to share memory among processes
    - complicated to set up
    - hard to manage because memory addresses of the same data aren't necessarily the same in
      different processes
- shared address space and lack of protection of data between threads makes the overhead associated
  much smaller
- flexibility of shared memory has downsides
    - view of data seen by each thread is consistent whenever accessed if data is accessed by
      multiple threads
- threads are most common place to turn for concurrency (but often the most complicated when considering
  other options)

## 1.2 Why use concurrency?
- separation of concerns
- performance

### Using concurrency for separation of concerns
- separate distinct areas of functionality

### Using concurrency for performance
- task parallelism - divide a single task into parts and run each in parallel (reducing total
  runtime)
- data parallelism - each unit of execution performs same operation on different parts of the
  data
- embarassingly parallel
- naturally parallel
- conveniently concurrent
- can use concurrency for performance by solving bigger problems (application of data parallelism
  at scale)

### When not to use concurrency
- when benefit is not worth the cost
- plan and do preliminary tests and benchmark
- threads are a limited resource

## 1.4 Concurrency and multithreading in C++
### History of multithreading in C++
### Concurrency support in the new standard
### Efficiency in the C++ Thread Library
- abstraction penalty
### Platform-specific facilities

## 1.4 Getting started
### Hello, Concurrent World
```
#include <iostream>
#include <thread>

void func()
{
    // do something
}

int main()
{
    std::thread t{func};
    t.join();
    ...
}
```
- each thread has to have an initial function for thread to begin execution
- main thread continues execution and join is used to ensure it does not exit until the
  created thread has launched and run

## 1.5 Summary


# Ch. 2 - Managing threads
## 2.1 Basic thread management

### Launching a thread
- started by constructing a std::thread object that specifies the task to run on that thread
- works with any callable type
- the supplied function object is copied into the storage belonging to the newly created thread of execution and
  invoked from there
- avoid C++'s most vexing parse in thread construction
    - use uniform initialization syntax
- can prefer lambdas
- when thread is started need to explicitly decide whether to
    - wait for it to finish (by joining)
    - leave it to run on its own (by detaching it)
    - if not done before std::thread object is destroyed then program will be terminated
        - std::thread destructor calls std::terminate()
    - only have to do this before the std::thread object is destroyed
- if program doesn't wait for thread to finish needs to ensure that the data accessed by the thread is valid until
  the thread has finished with it
- Listing 2.1 shows example of dangling reference
- can handle by making the thread function self-contained and copy the data into the thread rather
  than share the data
- be wary of objects containing pointers or references
- bad idea to create a thread within a function that has access to local variables in that
  function unless the thread is guaranteed to finish before the function exits

### Waiting for a thread to complete
- use join for straightforward waiting for thread to finish
- join is brute force
    - can use condition variables
    - can use futures
- join cleans up storage
- only call join once for a given thread
- use joinable to check if a thread can join

### Waiting in exceptional circumstances
- must call join or detach before thread is destroyed
- can call detach immediately in most cases
- must choose location of join carefully
- if an exception is thrown in a thread, a call to join may be skipped
    - use try/catch blocks
    - use a thread_guard and RAII to call join in destructor
        - mark copy/move as delete
```
~thread_guard()
{
    if (t.joinable()) {
        t.join();
    }
}
```

### Running threads in the background
- calling detach() on a std::thread object leaves the thread to run in the background
  with no direct means of communicating with it
    - ownership and control are passed over to the C++ runtime library which reclaims
      resources when thread exits
- often called daemon threads
- either for
    - long running actions
    - when there's another mechanism for identifying when the thread has completed
    - when the thread is used for a "fire and forget" task
    - multiple document user interactions

## 2.2 Passing arguments to a thread function
- passing arguments to the callable object or function
    - pass additional arguments to the std::thread constructor
- by default the arguments are copied into internal storage
    - can be accessed by the newly created thread of execution even if the corresponding parameter in the function is expecting a reference
- when passing arguments to threads that require conversion (constructors, etc, e.g. const char * to std::string)
  be aware that calling function may exit before argument has been constructed in new thread
  so may result in undefined behavior
- can also get situations where an object is copied into thread but what was desired was
  a reference
    - thread constructor is unaware of the type of argument that is supposed to be passed
      to callable and blindly copies in each argument
    - use std::ref(arg) to pass to thread constructor for references
- can pass member function pointers but must pass an object as the first argument (similar to
  std::bind)
```
std::thread t{&Class::member, &instance);
```
- may also run into move-only arguments
    - std::unique_ptr
    - etc.

## 2.3 Transferring ownership of a thread
- threads are movable but not copyable
    - requires std::move to reassign
- can return thread from a function without moving (moving is managed by compiler)
- requires moving into functions
- Listing 2.6 - scoped_thread builds on thread_guard but moves the passed thread
  into the class
- can use the move support to create containers of threads
    - rather than creating separate variables for threads and joining with them directly, they can be treated
      as a group

## 2.4 Choosing the number of threads at runtime
- std::thread::hardware_concurrency
- limit creating too many threads when an algorithm does not require it
- oversubscription - creating more threads than the hardware can support
    - context switching reduces performance
```
auto results = std::vector<T>(num_threads);
auto threads = std::vector<std::thread>(num_threads - 1);

auto block_start = first;
for (auto i = 0; i < (num_threads - 1); ++i) {
    auto block_end = block_start;
    std::advance(block_end, block_size);
    threads[i] = std::thread(accumulate_block<Iterator, T>(), block_start, block_end, std::ref(results[i]));
    block_start = block_end;
}
```
- must pass in a reference to return a result because cannot return a value from a thread
- alternative ways of returning results from threads are addressed through the use of futures

## 2.5 Identifying threads
- std::thread::id
- .get_id()
- if no associated thread, returns a default constructed thread::id which indicates no thread
- can also call std::this_thread::get_id()
- writing to output is implementation dependent
- (hashable)

## 2.6 Summary


# Ch. 3 - Sharing data between threads
## 3.1 Problems with sharing data between threads
- due to consequences of modifying data
- if all shared data is read-only, there's no problem, because the data read by one thread is unaffected
  by whether or not another thread is reading the same data
- invariants - assumptions that are required to always be true about a particular data structure (or
  other conditions)

### Race conditions
- any situation where the outcome depends on the relative ordering of execution of operations on two or more threads
    - the threads race to perform their respective operations
    - usually used to mean the problematic type
    - data races lead to undefined behavior
- generally occur when an operation depends on modification of two or more distinct pieces of data
    - if output instructions are sequential, testing may not show the issues until a large
      load is placed on the system
        - the number of times the operation is performed increases
        - the chance of the problematic execution sequence occurring also increases
    - debuggers affect the timing of the program and race conditions can disappear entirely
      because they are usually timing sensitive

### Avoiding problematic race conditions
- simplest option is to wrap data structure with a protection mechanism, to ensure that only the thread actually
  performing a modification can see the intermediate states where the invariants are broken
- lock-free programming - modify the design of data structure and its invariants so that modifications
  are done as a series of indivisible changes, each of which preserves the invariants
- software transactional memory (STM) - can handle the updates to the data structure as a transaction
    - required series of data modifications and reads is stored in a transaction log and then committed
      in a single step
    - if the commit can't proceed because the data structure has been modified by another
      thread, the transaction is restarted

### Protecting shared data with mutexes
- mutual exclusion - mechanism to mark data structures so that if any thread is running one of them any other thread
  that tried to access the data structure has to wait until the first thread is finished
    - makes it impossible for a thread to see a broken invariant except when it was the
      thread doing the modification
    - lock the mutex associated with the data
    - unlock the mutex when finished accessing the data structure
- risks deadlock

### Using mutexes in C++
- std::mutex and .lock and .unlock
- member locking functions are not recommended
- std::lock_guard template uses RAII to lock on construction and unlock on destruction
- best to group lock guard and mutex together in a class rather than access as global variables
    - any code that has access to protected data (via pointer or reference) encapsulated in such a class
      can access (and potentially modify) the protected data without locking the mutex
    - do not do this, use careful interface design

### Structuring code for protecting shared data
- also important to check that member functions don't pass such pointers or references in to
  functions they call that aren't under application programmer's control
    - those functions might store the pointer or reference in a place where it can later be
      used without the protection of the mutex
    - particularly dangerous in this regard are functions that are supplied at runtime via a function argument or
      other means
- general guideline
    - don't pass pointers and references to protected data outside the scope of the lock
        - by returning them from a function
        - storing them in externally visible memory
        - passing them as arguments to user-supplied functions

### Spotting race conditions inherent in interfaces
- can still get race conditions with proper simple protection
    - consider hand-over-hand locking required for linked list type structures
    - race conditions can still occur in lock free implementations
- design interfaces with care
    - which operations are absolutely essential?
    - which operations might run concurrently?
    - what happens when concurrent operations are interleaved?
- pop and return value from stack has specific issues

#### Option 1: Pass in a reference
- pass a reference to a variable to receive the popped value as an argument in the call to pop()
    - requires construction of stack's value type prior to call
    - may not be possible for certain types
    - requires that the stored type is assignable

#### Option 2: Require a no-throw copy constructor or move constructor
- only have an exception safety problem with a value-returning pop() if the return by value can throw an exception
    - many types have copy constructors that don't throw exceptions
    - more types will have a move constructor that doesn't throw exceptions due to r-value reference support
      (even if their copy constructor does)
- can restrict use of thread-safe stack to those types that can safely be returned by value without throwing an exception
    - not ideal
- can detect at compile time the existence of a copy or move constructor that doesn't throw an exception using the std::is_nothrow_copy_constructible and std::is_nothrow_move_constructible type traits
- limits the number of types that this will work with
    - more user-defined types with copy constructors that can throw and don't have move constructors than there are types with copy and/or move constructors that can't throw

#### Option 3: Return a pointer to the popped item
- return a pointer to the popped item rather than return the item by value
    - pointers can be freely copied without throwing an exception
        - avoided Cargill's exception problem
- disadvantage is that returning a pointer requires a means of managing the memory allocated to the object
    - overhead of such memory management can exceed the cost of just returning the type by value for simple types such as integers
- try to use std::shared_ptr for any interface that uses this option
    - avoids memory leaks
    - library is in full control of the memory allocation scheme
- can be important for optimization purposes
    - requiring that each object in the stack be allocated separately with new would impose quite an overhead compared to the original non-thread-safe version

#### Option 4: Provide both option 1 and either option 2 or 3
- flexibility is good in generic code
    - with option 2 or 3 relatively easy to provide option 1 as well

### Example definition of a thread-safe stack
- Listing 3.4
    - maximum safety with minimal interface
    - deleted assignment operator
- Listing 3.5
    - copy constructor locks the mutex in the source object and then copies the internal stack
        - does the copy in the constructor body rather than the member initializer list
            - ensures that the mutex is held across the copy
    - check for empty before trying to pop value in pop
    - in shared_ptr pop, allocate the return value before modifying the stack
```
std::shared_ptr<T> pop()
void pop(T& value)
```
- problematic race conditions in interfaces arise because of locking at too small a granularity
    - protection doesn't cover the entirety of the desired operation
- problems with mutexes can also arise from locking at too large a granularity
    - extremem single global mutex that protects all shared data
- can eliminate any performance benefits of concurrency in a system where there's a significant amount of shared data
- first versions of the Linux kernel that were designed to handle multiprocessor systems used a single global kernel lock

### Deadlock: the problem and a solution
- threads contested over locks on mutexes
    - each of a pair of threads needs to lock both of a pair of mutexes to perform some operation
    - each thread has one mutex and is waiting for the other
    - neither thread can proceed, because each is waiting for the other to release its mutex
- problem with having to lock two or more mutexes in order to perform an operation
- always lock the two mutexes in the same order
    - will never deadlock mutex A is always locked before mutex B
    - sometimes straightforward - the mutexes are serving different purposes
    - compex example - the mutexes are each protecting a separate instance of the same class
- e.g. operation that exchanges data between two instances of the same class
    - to ensure that the data is exchanged correctly, without being affected by concurrent modifications,
      mutexes on both instances must be locked
    - if a fixed order is chosen this can backfire
        - if two threads try to exchange data between the same two instances with the parameters swapped - have a deadlock
- C++ Standard Library solution - std::lock
    - function that can lock two or more mutexes at once without risk of deadlock
- Listing 3.6
    - arguments are checked to ensure they are different instances
        - attempting to acquire a lock on a std::mutex when it is already held is undefined behavior
            - mutex that does permit multiple locks by the same thread is provided in the form of std::recursive_mutex
        - call to std::lock() locks the two mutexes
        - two std::lock_guard instances are constructed - one for each mutex
            - std::adopt_lock parameter is supplied in addition to the mutex to indicate to the std::lock_guard objects that the mutexes are already locked
              and they should just adopt the ownership of the existing lock on the mutex rather than attempt to lock the mutex in the constructor
            - ensures that the mutexes are correctly unlocked on function exit in the general case where the protected operation might throw an exception
            - also allows for a simple return
            - NOTE: locking either lhs.m or rhs.m inside the call to std::lock can throw an exception
                - exception is propagated out of std::lock
                - if std::lock has successfully acquired a lock on one mutex and an exception is thrown when it tries to acquire a lock on the other mutex,
                  first lock is released automatically
                    - std::lock provides all-or-nothing semantics with regard to locking the supplied mutexes
- std::lock can help avoid deadlock in cases where need to acquire two or more locks together
    - doesn't help if they're acquired separately
    - have to rely on discipline as developers to avoid deadlock
- deadlocks are one of the nastiest problems to encounter in multithreaded code and are often unpredictable

### Further guidelines for avoiding deadlock
- deadlock can occur while waiting for any shared resource
- can create deadlock with two threads and no locks just by having each thread call join() on the std::thread object for the other
    - neither thread can make progress because it's waiting for the other to finish
    - simple cycle can occur anywhere that a thread can wait for another thread to perform some action if the other thread can simultaneously be waiting for the first thread
    - isn't limited to two threads
        - a cycle of three or more threads will still cause deadlock
- summary - don't wait for another thread if there's a chance it's already waiting on current thread

#### Avoid nested locks
- don't acquire a lock if it is already held
    - impossible to get a deadlock from the lock usage alone because each thread only ever holds a single lock
- can still get deadlock from other things (like the threads waiting for each other), but mutex locks are probably the most common cause of deadlock
- acquire multiple locks as a single action with std::lock in order to acquire them without deadlock

#### Avoid calling user-supplied code while holding a lock
- no idea what user-supplied code could do
    - could acquire a lock
        - violates the guideline on avoiding nested locks and could get deadlock
- sometimes unavoidable
    - for generic code every operation on the parameter type or types is user-supplied code

#### Acquire locks in a fixed order
- if must acquire two or more locks and can't acquire them as a single operation with std::lock
    - acquire them in the same order in every thread
    - key is to define the order in a way that's consistent between threads
    - previous stack definition - mutex is internal to each stack instance but the operations on the data items stored in a stack require calling user-supplied code
        - can add the constraint that none of the operations on the data items stored in the stack should perform any operation on the stack itself
        - requires user to handle but uncommon for the data stored in a container to access that container
            - obvious when it happens
    - in some cases can lock the mutexes simultaneously
        - not always possible
    - for linked list one possibility for protecting the list is to have a mutex per node
        - threads must acquire a lock on every node they're interested in to access list
        - threads must acquire the lock on three nodes to delete an item
            - node being deleted and the nodes on either side
                - all being modified in some way
            - to traverse the list a thread must keep hold of the lock on the current node while it acquires the lock on the next one in the sequence
                - ensures that the next pointer isn't modified while transitioning
            - once the lock on the next node has been acquired, the lock on the first can be released
    - hand-over-hand locking style allows multiple threads to access the list
        - requires each to access a different node
    - nodes must always be locked in the same order to prevent deadlock
        - if two threads tried to traverse the list in reverse order using hand-over-hand locking, they could deadlock with each other in the middle of the list
        - if nodes A and B are adjacent in the list, the thread going one way will try to hold the lock on node A and try to acquire the lock on node B
        - a thread going the other way would be holding the lock on node B and trying to acquire the lock on node A
            - could cause deadlock
    - when deleting node B that lies between nodes A and C, if that thread acquires the lock on B before the locks on A and C, it has the potential to deadlock
      with a thread traversing the list
        - thread would try to lock either A or C first (depending on the direction of traversal) but would then find that it couldn't obtain a lock on B because
          the thread doing the deleting was holding the lock on B and trying to acquire the locks on A and C
    - define an order of traversal
        - thread must always lock A before B and B before C
        - would eliminate the possibility of deadlock at the expense of disallowing reverse traversal
    - similar conventions can often be established for other data structures

#### Use a lock hierarchy
- particular case of defining lock ordering
- divide application into layers and identify all the mutexes that may be locked in any given layer
    - when code tries to lock a mutex, it isn't permitted to lock that mutex if it already holds a lock from a lower layer
    - can check this at runtime by assigning layer numbers to each mutex and keeping a record of which mutexes are locked by each thread
- Listing 3.7

#### Extending these guidelines beyond locks
- deadlock doesn't just occur with locks
    - can occur with any synchronization construct that can lead to a wait cycle
- avoid acquiring nested locks if possible
- bad idea to wait for a thread while holding a lock
    - thread might need to acquire the lock in order to proceed
- if waiting for a thread to finish
    - worth identifying a thread hierarchy
        - thread waits only for threads lower down the hierarchy
    - ensure that threads are joined in the same function that started them
- std::lock() and std:: lock_guard cover most of the cases of simple locking
    - provides the std::unique_lock template
        - class template parameterized on the mutex type
        - provides RAII-style lock management but with a bit more flexibility

### Flexible locking with std::unique_lock
- std::unique_lock provides more flexibility by relaxing the invariants
    - an instance doesn't always own the mutex that it's associated with
    - can pass std::adopt_lock as a second argument to the constructor to have the lock object manage the lock on a mutex
    - can pass std::defer_lock as the second argument to indicate that the mutex should remain unlocked on construction
    - lock can then be acquired later by calling lock() on the std::unique_lock object (not the mutex) or by passing the
      std::unique_lock object itself to std::lock()
    - using std::unique_lock and std::defer_lock rather than std::lock_guard and std::adopt_lock
        - std::unique_lock takes more space and is a fraction slower to use than std::lock_guard
        - flexibility of allowing a std::unique_lock instance not to own the mutex comes at the price that information has to
          be stored, and it has to be updated
- Listing 3.9
    - std::unique_lock objects could be passed to std::lock() because std::unique_lock provides lock(), try_lock(), and unlock()
    - forward to the member functions of the same name on the underlying mutex to do the actual work and just update a flag inside the std::unique_lock instance to indicate
      whether the mutex is currently owned by that instance
    - flag is necessary in order to ensure that unlock() is called correctly in the destructor
    - destructor must call unlock() if the instance does own the mutex and shouldn't if it doesn't
    - flag can be queried by calling the owns_lock() member function
    - size of a std::unique_lock object is larger than that of a std::lock_guard object
        - slight performance penalty when using std::unique_lock over std::lock_guard
        because the flag has to be updated or checked, as appropriate. If
    - prefer if std::lock_guard is sufficient for needs
    - cases where std::unique_lock is a better fit
        - deferred locking
        - case is where the ownership of the lock needs to be transferred from one scope to another

### Transferring mutex ownership between scopes
- ownership of a mutex can be transferred between instances by moving the instances around because std::unique_lock instances don't have to own their associated mutexes
    - transfer is automatic in some cases
        - returning an instance from a function
    - have to do it explicitly by calling std::move() in some cases
        - depends on whether the source is an lvalue or an rvalue
    - transfer is automatic if the source is an rvalue
    - transfer must be done explicitly for an lvalue in order to avoid accidentally transferring ownership away from a variable
    - std::unique_lock is movable but not copyable
- can allow a function to lock a mutex and transfer ownership of that lock to the caller, so the caller can then perform additional
  actions under the protection of the same lock
```
std::unique_lock<std::mutex> get_lock()
{
    extern std::mutex some_mutex;
    std::unique_lock<std::mutex> lk(some_mutex);
    prepare_data();
    return lk;
}

void process_data()
{
    std::unique_lock<std::mutex> lk(get_lock());
    do_something();
}
```
- lk can be returned directly without a call to std:move() because it is an automatic variable declared
  within the function
    - compiler handles calling move constructor
- process_data() can then transfer ownership directly into its own std::unique_lock instance
- do_something() can rely on the data being correctly prepared without another thread altering the data
- pattern is used where the mutex to be locked is dependent on the current state of the program or on an argument
    - e.g. where the lock isn't returned directly but is a data member of a gateway class used to ensure correctly locked access to
      some protected data
    - all access to the data is through this gateway class
        - to access data obtain an instance of the gateway class by calling a function such as get_lock()
        - then access the data through member functions of the gateway object
        - destroy the gateway object, which releases the lock and allows other threads to access the protected data
        - gateway object can be movable (so that it can be returned from a function)
            - lock object data member also needs to be movable
- std::unique_lock also allows instances to relinquish their locks before they're destroyed
    - use the unlock() member function
        - std::unique_lock supports the same basic set of member functions for locking and unlocking as a mutex does
        - can be used with generic functions such as std::lock
        - ability to release a lock before the std::unique_lock instance is destroyed means
            - can optionally release it in a specific code branch if it's apparent that the lock is no longer required
    - holding a lock for longer than required can cause performance hit

### Locking at an appropriate granularity
- lock granularity is a vague term to describe the amount of data protected by a single lock
    - fine-grained lock protects a small amount of data
    - coarse-grained lock protects a large amount of data
- important to choose a sufficiently coarse lock granularity to ensure the required data is protected
- important to ensure that a lock is held only for the operations that actually require it

- if any thread holds lock for longer than necessary when multiple threads are waiting for the same resource
  it will increase the total time spent waiting
    - lock a mutex only while actually accessing the shared data
    - try to do any processing of the data outside the lock
    - don't do any really time-consuming activities like file I/O while holding a lock
    - file I/O is typically hundreds (if not thousands) of times slower than reading or writing the same volume of data from memory
        - unless the lock is really intended to protect access to the file, performing I/O while holding the lock will delay other
          threads unnecessarily
    - std::unique_lock works well in this situation
        - can call unlock() when the code no longer needs access to the shared data and then call lock() again if access is required later in the code
```
void get_and_process_data()
{
    std::unique_lock<std::mutex> my_lock(the_mutex);
    some_class data_to_process=get_next_data_chunk();
    my_lock.unlock();
    result_type result=process(data_to_process);
    my_lock.lock();
    write_result(data_to_process,result);
}
```
- manually unlock it and then lock it again afterward
- if there is one mutex protecting an entire data structure
    - likely to be more contention for the lock
    - less potential for reducing the time that the lock is held
- locking at an appropriate granularity isn't only about the amount of data locked
    - also concerns how long a lock is held and what operations are performed while the lock is held
    - a lock should be held for only the minimum possible time needed to perform the required operations
    - time-consuming operations such as acquiring another lock or waiting for I/O to complete shouldn't be
      done while holding a lock unless absolutely necessary
- Listing 3.10
- function retrieves the value while protecting it with a lock
    - comparison operator then compares the retrieved values
    . Note, however, that as well as
    - reduces the locking periods so that only one lock is held at a time
        - eliminates the possibility of deadlock
    - changed the semantics of the operation compared to holding both locks together
        - means that the value of lhs.some_detail at one point in time is equal to the value of rhs.some_detail at another point in time
          if operator returns true
        - two values could have been changed in any way in between the two reads
            - values could have been swapped
        - equality comparison might return true to indicate that the values were equal even though there was never an instant in time when
          the values were actually equal
- be careful when making such changes that the semantics of an operation are not changed in a problematic fashion
    - exposed to race conditions when not holding the required locks for the entire duration of an operation
- may be appropriate to use an alternative mechanism when there isn't an appropriate level of granularity because not all accesses to the
  data structure require the same level of protection

## 3.3 Alternative facilities for protecting shared data
- mutexes are the most general mechanism
- alternatives for protecting shared data
- when the shared data only needs protection from concurrent access while it's being initialized
  (no explicit synchronization required afterwards)
- e.g. because the data is read-only once created, and so there are no possible synchronization issues
- e.g. because the necessary protection is performed implicitly as part of the operations on the data
- locking a mutex after the data has been initialized just to protect the initialization is unnecessary
- C++ Standard provides a mechanism purely for protecting shared data during initialization

### Protecting shared data during initialization
- for shared resource that's expensive to construct
    - e.g. a database connection
    - e.g. large memory allocation
- lazy initialization is common in single-threaded code
    - each operation that requires the resource checks to see if it has been initialized and then initializes it before use if not
```
std::shared_ptr<some_resource> resource_ptr;
void foo()
{
    if(!resource_ptr) {
        resource_ptr.reset(new some_resource);
    }
    resource_ptr->do_something();
}
```
- only part that needs protection while converting to multithreaded code is the initialization if the shared resource itself is safe for concurrent access
    - naive approach can cause unnecessary serialization of threads using the resource
        - each thread must wait on the mutex in order to check whether the resource has already been initialized
- Listing 3.11
- thread-safe lazy initialization using a mutex is common and leads to problematic and unnecessary serialization
    - one attempt at improvement is infamous Double-Checked Locking pattern
        - pointer is first read without acquiring the lock
        - lock is acquired only if the pointer is NULL
        - pointer is then checked again once the lock has been acquired (double-checked part)
          in case another thread has done the initialization between the first check and this thread acquiring the lock
```
void undefined_behaviour_with_double_checked_locking()
{
    if(!resource_ptr) {
        std::lock_guard<std::mutex> lk(resource_mutex);
        if(!resource_ptr) {
            resource_ptr.reset(new some_resource);
        }
    }
    resource_ptr->do_something();
}
```
- pattern has the potential for nasty race conditions
    - read outside the lock isn't synchronized with the write done by another thread inside the lock
    - creates a race condition for the pointer and the object pointed to
        - even if a thread sees the pointer written by another thread, it might not see the newly created instance
          of some_resource, resulting in the call to do_something() operating on incorrect values
    - an example of the type of race condition defined as a data race by the C++ Standard and thus specified as undefined behavior
    - C++ Standard Library provides std::once_flag and std::call_once to handle this situation
        - every thread can just use std::call_once rather than locking a mutex and explicitly checking the pointer
            - knows pointer will have been initialized by some thread in a properly synchronized fashion by the time std::call_once returns
            . Use of std::call_once will
        - typically lower overhead for use of std::call_once than using a mutex explicitly
            - especially when the initialization has already been done
        - should be preferred when appropriate
- thread-safe lazy initialization rewritten to use std::call_once
    - initialization is done by calling a function, but it
    - could have been done with an instance of a class with a function call operator
    - std::call_once works with any function or callable object like most of the functions in the standard library that take functions or predicates as arguments
    - initialization is called exactly once
    - both the std::once_flag and data being initialized are namespace-scope objects
    - std::call_once() can easily be used for lazy initialization of class members
```
std::shared_ptr<some_resource> resource_ptr;
std::once_flag resource_flag;
void init_resource()
{
    resource_ptr.reset(new some_resource);
}
void foo() {
    std::call_once(resource_flag,init_resource);
    resource_ptr->do_something();
}
```
- Listing 3.12
    - initialization is done either by the first call to send_data() or by the first call to receive_data()
    - use of the member function open_connection() to initialize the data requires that the this pointer be passed in
    - done by passing an additional argument to std::call_once() as for other functions in the Standard Library that accept callable objects
      such as the constructor for std::thread and std::bind()
    - NOTE: like std::mutex, std::once_flag instances can't be copied or moved
        - have to explicitly define these special member functions if they are required to be used as class members
- local variable declared with static leads to potential race condition
    - initialization of such a variable is defined to occur the first time control passes through its declaration
    - potential for a race condition to define first for multiple threads calling the function
    - pre-C++11 compilers
        - multiple threads may believe they're first and try to initialize the variable
        - threads may try to use it after initialization has started on another thread but before it's
          finished
    - solved for C++11
        - initialization is defined to happen on exactly one thread, and no other threads will proceed until that initialization is complete
        - race condition is just over which thread gets to do the initialization rather than anything more problematic
        - following can be used as an alternative to std::call_once for those cases where a single global instance is required
        - initialization is guaranteed to be thread-safe, multiple threads can call get_instance safely
```
class my_class;
my_class& get_my_class_instance()
{
    static my_class instance;
    return instance;
}
```
- more general scenario is rarely updated data structure
    - protecting data only for initialization is special case
    - data structure is read-only for most of the time
        - can be read by multiple threads concurrently
    - data structure may need updating occasionally

### Protecting rarely updated data structures
- for certain data structures updates are rare
    - if data structure is to be accessed from multiple threads it will need to be appropriately protected during updates to
      ensure that none of the threads reading the cache see a broken data structure
- such an update requires that the thread doing the update have exclusive access to the data structure until it has completed the operation
    - in the absence of a special-purpose data structure that exactly fits the desired usage and is specially designed for concurrent updates and reads
    - when update is complete the data structure is again safe for multiple threads to access concurrently
    - using a std::mutex to protect the data structure is overly pessimistic
        - will eliminate possible concurrency in reading the data structure when it isn't undergoing modification
    - need a reader-writer mutex
        - allows for exclusive access by a single "writer" thread or shared
        - allows for concurrent access by multiple "reader" threads
- C++ Standard Library doesn't provide such a mutex out of the box
    - one was proposed to the Standards Committee
- implementation provided by the Boost library based on proposal
    - doesn't solve all related problems
    - performance is dependent on the number of processors involved and the relative workloads of the reader and updater threads
    - important to profile the performance of the code on the target system to ensure that there's actually a benefit to the additional complexity
- boost::shared_mutex
    - std::lock_guard<boost::shared_mutex> and std::unique_lock<boost::shared_mutex> can be used for the locking for update operations
        - ensure exclusive access
    - threads that don't need to update the data structure can instead use boost::shared_lock<boost::shared_mutex> to obtain shared access
        - used same as std::unique_lock
            - multiple threads may have a shared lock on the same boost::shared_mutex at the same time
            - only constraint is that if any thread has a shared lock, a thread that tries to acquire an exclusive lock will block until all other threads
              have relinquished their locks
            - if any thread has an exclusive lock, no other thread may acquire a shared or exclusive lock until the first thread has relinquished its lock
- Listing 3.13 (std::map for DNS cache with boost::shared_mutex)
    - find_entry() uses an instance of boost::shared_lock<> to protect it for shared, read-only access
        - multiple threads can call find_entry() simultaneously without problems
    - update_or_add_entry() uses an instance of std::lock_guard<> to provide exclusive access while the table is updated
        - other threads prevented from doing updates in a call update_or_add_entry()
        - threads that call find_entry() are blocked as well

### Recursive Locking
- error for a thread to try to lock a mutex it already owns with std::mutex
    - attempting to do so will result in undefined behavior
- some cases need a thread to reacquire the same mutex several times without having first released it
    - C++ Standard Library provides std::recursive_mutex
    - works like std::mutex
        - can acquire multiple locks on a single instance from the same thread
        - must release all locks before the mutex can be locked by another thread
            - lock() is called three times, must also call unlock() three times
    - correct use of std::lock_guard<std::recursive_mutex> and std::unique_lock<std::recursive_mutex> will handle this
- probably need to change design instead if it seems like a recursive mutex is needed in most cases
- common use of recursive mutexes is where a class is designed to be accessible from multiple threads concurrently
    - uses a mutex to protect member data
    - each public member function locks the mutex, does the work, and then unlocks the mutex
    - sometimes necessary for one public member function to call another as part of its operation
        - second member function will also try to lock the mutex leading to undefined behavior
    - can change the mutex to a recursive mutex
        - allows the mutex lock in the second member function to succeed and the function to proceed
    - such usage is not recommended
        - can lead to sloppy thinking and bad design
    - class invariants are typically broken while the lock is held
        - second member function needs to work even when called with the invariants broken
    - usually better to extract a new private member function that's called from both member functions
      which does not lock the mutex
        - assumes lock is already locked
    - can then carefully about the circumstances under which new function will be called and the state of the data
      under those circumstances

## 3.4 Summary
- problematic race conditions can be disastrous when sharing data between threads
    - use std::mutex and careful interface design to avoid them
- mutexes don't solve all problems
    - deadlock
- C++ Standard Library provides std::lock() to help solve mutex problems
- further techniques for avoiding deadlock
- transferring lock ownership and issues surrounding choosing the appropriate granularity for locking
- alternative data-protection facilities provided for specific scenarios
    - std::call_once()
    - boost::shared_mutex
- not covered: waiting for input from other threads
    - thread-safe stack just throws an exception if the stack is empty
        - if one thread wanted to wait for another thread to push a value on the stack it would have to repeatedly try to pop a
          value, retrying if an exception gets thrown
        - consumes valuable processing time in performing the check without actually making any progress
        - constant checking might hamper progress by preventing the other threads in the system from running
    - need a way for a thread to wait for another thread to complete a task without consuming CPU time in the process
