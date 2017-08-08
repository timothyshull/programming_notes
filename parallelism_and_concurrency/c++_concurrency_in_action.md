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
    - can be accessed by the newly created thread of execution even if the corresponding parameter in the function is
      expecting a reference
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
- can detect at compile time the existence of a copy or move constructor that doesn't throw an exception using the
  std::is_nothrow_copy_constructible and std::is_nothrow_move_constructible type traits
- limits the number of types that this will work with
    - more user-defined types with copy constructors that can throw and don't have move constructors than there are
      types with copy and/or move constructors that can't throw

#### Option 3: Return a pointer to the popped item
- return a pointer to the popped item rather than return the item by value
    - pointers can be freely copied without throwing an exception
        - avoided Cargill's exception problem
- disadvantage is that returning a pointer requires a means of managing the memory allocated to the object
    - overhead of such memory management can exceed the cost of just returning the type by value for simple types such
      as integers
- try to use std::shared_ptr for any interface that uses this option
    - avoids memory leaks
    - library is in full control of the memory allocation scheme
- can be important for optimization purposes
    - requiring that each object in the stack be allocated separately with new would impose quite an overhead compared
      to the original non-thread-safe version

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
- first versions of the Linux kernel that were designed to handle multiprocessor systems used a single global kernel
  lock

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
        - if two threads try to exchange data between the same two instances with the parameters swapped -
          have a deadlock
- C++ Standard Library solution - std::lock
    - function that can lock two or more mutexes at once without risk of deadlock
- Listing 3.6
    - arguments are checked to ensure they are different instances
        - attempting to acquire a lock on a std::mutex when it is already held is undefined behavior
            - mutex that does permit multiple locks by the same thread is provided in the form of std::recursive_mutex
        - call to std::lock() locks the two mutexes
        - two std::lock_guard instances are constructed - one for each mutex
            - std::adopt_lock parameter is supplied in addition to the mutex to indicate to the std::lock_guard objects
              that the mutexes are already locked  and they should just adopt the ownership of the existing lock on
              the mutex rather than attempt to lock the mutex in the constructor
            - ensures that the mutexes are correctly unlocked on function exit in the general case where the protected
              operation might throw an exception
            - also allows for a simple return
            - NOTE: locking either lhs.m or rhs.m inside the call to std::lock can throw an exception
                - exception is propagated out of std::lock
                - if std::lock has successfully acquired a lock on one mutex and an exception is thrown when it tries
                  to acquire a lock on the other mutex, first lock is released automatically
                    - std::lock provides all-or-nothing semantics with regard to locking the supplied mutexes
- std::lock can help avoid deadlock in cases where need to acquire two or more locks together
    - doesn't help if they're acquired separately
    - have to rely on discipline as developers to avoid deadlock
- deadlocks are one of the nastiest problems to encounter in multithreaded code and are often unpredictable

### Further guidelines for avoiding deadlock
- deadlock can occur while waiting for any shared resource
- can create deadlock with two threads and no locks just by having each thread call join() on the std::thread object
  for the other
    - neither thread can make progress because it's waiting for the other to finish
    - simple cycle can occur anywhere that a thread can wait for another thread to perform some action if the other
      thread can simultaneously be waiting for the first thread
    - isn't limited to two threads
        - a cycle of three or more threads will still cause deadlock
- summary - don't wait for another thread if there's a chance it's already waiting on current thread

#### Avoid nested locks
- don't acquire a lock if it is already held
    - impossible to get a deadlock from the lock usage alone because each thread only ever holds a single lock
- can still get deadlock from other things (like the threads waiting for each other), but mutex locks are probably the
  most common cause of deadlock
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
    - previous stack definition - mutex is internal to each stack instance but the operations on the data items stored
      in a stack require calling user-supplied code
        - can add the constraint that none of the operations on the data items stored in the stack should perform any
          operation on the stack itself
        - requires user to handle but uncommon for the data stored in a container to access that container
            - obvious when it happens
    - in some cases can lock the mutexes simultaneously
        - not always possible
    - for linked list one possibility for protecting the list is to have a mutex per node
        - threads must acquire a lock on every node they're interested in to access list
        - threads must acquire the lock on three nodes to delete an item
            - node being deleted and the nodes on either side
                - all being modified in some way
            - to traverse the list a thread must keep hold of the lock on the current node while it acquires the lock
              on the next one in the sequence
                - ensures that the next pointer isn't modified while transitioning
            - once the lock on the next node has been acquired, the lock on the first can be released
    - hand-over-hand locking style allows multiple threads to access the list
        - requires each to access a different node
    - nodes must always be locked in the same order to prevent deadlock
        - if two threads tried to traverse the list in reverse order using hand-over-hand locking, they could deadlock
          with each other in the middle of the list
        - if nodes A and B are adjacent in the list, the thread going one way will try to hold the lock on node A and
          try to acquire the lock on node B
        - a thread going the other way would be holding the lock on node B and trying to acquire the lock on node A
            - could cause deadlock
    - when deleting node B that lies between nodes A and C, if that thread acquires the lock on B before the locks on
      A and C, it has the potential to deadlock with a thread traversing the list
        - thread would try to lock either A or C first (depending on the direction of traversal) but would then find
          that it couldn't obtain a lock on B because  the thread doing the deleting was holding the lock on B and
          trying to acquire the locks on A and C
    - define an order of traversal
        - thread must always lock A before B and B before C
        - would eliminate the possibility of deadlock at the expense of disallowing reverse traversal
    - similar conventions can often be established for other data structures

#### Use a lock hierarchy
- particular case of defining lock ordering
- divide application into layers and identify all the mutexes that may be locked in any given layer
    - when code tries to lock a mutex, it isn't permitted to lock that mutex if it already holds a lock from a lower
      layer
    - can check this at runtime by assigning layer numbers to each mutex and keeping a record of which mutexes are
      locked by each thread
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
        - flexibility of allowing a std::unique_lock instance not to own the mutex comes at the price that information
          has to be stored, and it has to be updated
- Listing 3.9
    - std::unique_lock objects could be passed to std::lock() because std::unique_lock provides lock(), try_lock(), and
      unlock()
    - forward to the member functions of the same name on the underlying mutex to do the actual work and just update a
      flag inside the std::unique_lock instance to indicate  whether the mutex is currently owned by that instance
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
- ownership of a mutex can be transferred between instances by moving the instances around because std::unique_lock
  instances don't have to own their associated mutexes
    - transfer is automatic in some cases
        - returning an instance from a function
    - have to do it explicitly by calling std::move() in some cases
        - depends on whether the source is an lvalue or an rvalue
    - transfer is automatic if the source is an rvalue
    - transfer must be done explicitly for an lvalue in order to avoid accidentally transferring ownership away from
      a variable
    - std::unique_lock is movable but not copyable
- can allow a function to lock a mutex and transfer ownership of that lock to the caller, so the caller can then
  perform additional actions under the protection of the same lock
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
    - e.g. where the lock isn't returned directly but is a data member of a gateway class used to ensure correctly
      locked access to some protected data
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
    - file I/O is typically hundreds (if not thousands) of times slower than reading or writing the same volume of data
      from memory
        - unless the lock is really intended to protect access to the file, performing I/O while holding the lock will
          delay other threads unnecessarily
    - std::unique_lock works well in this situation
        - can call unlock() when the code no longer needs access to the shared data and then call lock() again if
          access is required later in the code
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
        - means that the value of lhs.some_detail at one point in time is equal to the value of rhs.some_detail at
          another point in time if operator returns true
        - two values could have been changed in any way in between the two reads
            - values could have been swapped
        - equality comparison might return true to indicate that the values were equal even though there was never an
          instant in time when the values were actually equal
- be careful when making such changes that the semantics of an operation are not changed in a problematic fashion
    - exposed to race conditions when not holding the required locks for the entire duration of an operation
- may be appropriate to use an alternative mechanism when there isn't an appropriate level of granularity because not
  all accesses to the data structure require the same level of protection

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
    - each operation that requires the resource checks to see if it has been initialized and then initializes it before
      use if not
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
- only part that needs protection while converting to multithreaded code is the initialization if the shared resource
  itself is safe for concurrent access
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
    - an example of the type of race condition defined as a data race by the C++ Standard and thus specified as
      undefined behavior
    - C++ Standard Library provides std::once_flag and std::call_once to handle this situation
        - every thread can just use std::call_once rather than locking a mutex and explicitly checking the pointer
            - knows pointer will have been initialized by some thread in a properly synchronized fashion by the
              time std::call_once returns
        - typically lower overhead for use of std::call_once than using a mutex explicitly
            - especially when the initialization has already been done
        - should be preferred when appropriate
- thread-safe lazy initialization rewritten to use std::call_once
    - initialization is done by calling a function, but it
    - could have been done with an instance of a class with a function call operator
    - std::call_once works with any function or callable object like most of the functions in the standard library
      that take functions or predicates as arguments
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
    - done by passing an additional argument to std::call_once() as for other functions in the Standard Library that
      accept callable objects
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
        - initialization is defined to happen on exactly one thread, and no other threads will proceed until that
          initialization is complete
        - race condition is just over which thread gets to do the initialization rather than anything more problematic
        - following can be used as an alternative to std::call_once for those cases where a single global instance is
          required
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
    - if data structure is to be accessed from multiple threads it will need to be appropriately protected during
      updates to ensure that none of the threads reading the cache see a broken data structure
- such an update requires that the thread doing the update have exclusive access to the data structure until it has
  completed the operation
    - in the absence of a special-purpose data structure that exactly fits the desired usage and is specially designed
      for concurrent updates and reads
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
    - performance is dependent on the number of processors involved and the relative workloads of the reader and
      updater threads
    - important to profile the performance of the code on the target system to ensure that there's actually a benefit
      to the additional complexity
- boost::shared_mutex
    - std::lock_guard<boost::shared_mutex> and std::unique_lock<boost::shared_mutex> can be used for the locking for
      update operations
        - ensure exclusive access
    - threads that don't need to update the data structure can instead use boost::shared_lock<boost::shared_mutex> to
      obtain shared access
        - used same as std::unique_lock
            - multiple threads may have a shared lock on the same boost::shared_mutex at the same time
            - only constraint is that if any thread has a shared lock, a thread that tries to acquire an exclusive lock
              will block until all other threads have relinquished their locks
            - if any thread has an exclusive lock, no other thread may acquire a shared or exclusive lock until the
              first thread has relinquished its lock
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
        - if one thread wanted to wait for another thread to push a value on the stack it would have to repeatedly
          try to pop a value, retrying if an exception gets thrown
        - consumes valuable processing time in performing the check without actually making any progress
        - constant checking might hamper progress by preventing the other threads in the system from running
    - need a way for a thread to wait for another thread to complete a task without consuming CPU time in the process


# Ch. 4 - Synchronizing concurrent operations
## 4.1 Waiting for an event or other condition
- options if one thread is waiting for a second thread to complete a task
1. could just keep checking a flag in shared data (protected by a mutex) and have the second thread set the flag when it
  completes the task
    - wasteful
        - thread consumes valuable processing time repeatedly checking the flag
        - when the mutex is locked by the waiting thread, it can't be locked by any other thread
    - works against the thread doing the waiting
        - limit the resources available to the thread being waited for and even prevent it from setting the flag when
          finished
    - waiting thread is consuming resources that could be used by other threads in the system and may end up waiting
      longer than necessary
2. have the waiting thread sleep for small periods between the checks using the std::this_thread::sleep_for()
   function
```
bool flag;
std::mutex m;
void wait_for_flag()
{
    std::unique_lock<std::mutex> lk(m);
    while(!flag)
    {
        lk.unlock();
        std::this_thread::sleep_for(std::chrono::milliseconds(100));
        lk.lock();
    }
}
```
- function unlocks the mutex before the sleep and locks it again afterward so another thread gets a chance to acquire
  it and set the flag
    - thread doesn't waste processing time while it's sleeping
        - difficult to get the sleep period right
            - too short a sleep in between checks and the thread still wastes processing time checking
            - too long a sleep and the thread will keep on sleeping even when the task it's waiting for is complete
              introducing a delay
    - rare that this oversleeping will have a direct impact on the operation of the program
        - could mean dropped frames in a fast-paced game or overrunning a time slice in a real-time application
3. preferred option is to use the facilities from the C++ Standard Library to wait for the event itself
    - most basic mechanism for waiting for an event to be triggered by another thread (such as the presence of
      additional work in the pipeline mentioned previously)
      is the condition variable
    - condition variable is associated with some event or other condition
        - one or more threads can wait for that condition to be satisfied
        - when some thread has determined that the condition is satisfied it can then notify one or more of the threads
          waiting on the condition variable
          in order to wake them up and allow them to continue processing

### Waiting for a condition with condition variables
- Standard C++ Library provides two implementations of a condition variable
    - std::condition_variable
    - std::condition_variable_any
- declared in the <condition_variable> header
- both need to work with a mutex in order to provide appropriate synchronization
    - std::condition_variable is limited to working with std::mutex
    - std::condition_variable_any can work with anything that meets some minimal criteria for being mutex-like
        - more general
        - potential for additional costs in terms of size, performance, or operating system resources
- prefer std::condition_variable
- Listing 4.1
    - queue that's used to pass the data between the two threads
        - when the data is ready, the thread preparing the data locks the mutex protecting the queue using a
          std::lock_guard and pushes the data onto the queue
        - calls the notify_one() member function on the std::condition_variable instance to notify the waiting thread
          (if there is one)
    - also have the processing thread that first locks the mutex using a std::unique_lock
    - then calls wait() on the std::condition_variable, passing in the lock object and a lambda function that expresses
      the condition being waited for
        - simple lambda function `[]{ return !data_queue.empty(); }` checks to see if the data_queue is not empty()
          i.e. there's some data in the queue ready for processing
    - wait() then checks the condition (by calling the supplied lambda function) and returns if it's satisfied
      (the lambda function returned true)
    - if the condition isn't satisfied (the lambda function returned false), wait() unlocks the mutex and puts the
      thread in a blocked or waiting state
    - when the condition variable is notified by a call to notify_one() from the data-preparation thread
        - thread wakes from sleep (unblocks)
        - reacquires the lock on the mutex
        - checks the condition again
        - returns from wait() with the mutex still locked if the condition has been satisfied
        - otherwise the thread unlocks the mutex and resumes waiting
            - why std::unique_lock is needed rather than the std::lock_guard
                - waiting thread must unlock the mutex while it's waiting and lock it again afterward
                  (std::lock_guard can't handle this)
                - if the mutex remained locked while the thread was sleeping, the data-preparation thread wouldn't be
                  able to lock the mutex to add an item to the queue, and the waiting thread would never be able to
                  see its condition satisfied
    - simple lambda function for the wait which checks to see if the queue is not empty
        - any function or callable object could be passed
        - a condition variable may check the supplied condition any number of times during a call to wait()
            - always does so with the mutex locked and will return immediately if (and only if) the function provided
              to test the condition returns true
             - called a spurious wake when the waiting thread reacquires the mutex and checks the condition if it isn't
               in direct response to a notification from another thread
             - isn't advisable to use a function with side effects for the condition check because number and frequency
               of spurious wakes are by definition indeterminate
                - side effects may occur multiple times
    - flexibility to unlock a std::unique_lock can also be used once the data is available to process but before
      processing it
        - processing data can potentially be a time-consuming operation
        - bad idea to hold a lock on a mutex for longer than necessary
- common to use a queue to transfer data between threads
    - synchronization can be limited to the queue itself
        - greatly reduces the possible number of synchronization problems and race conditions

### Building a thread-safe queue with condition variables
- look at std::queue<> container adaptor in C++ Standard Library for inspiration to design a generic queue
- Listing 4.2
- three groups of operations (ignoring the construction, assignment and swap operations)
    - queries to state of the whole queue (empty() and size())
    - queries the elements of the queue (front() and back())
    - modifications to queue (push(), pop() and emplace())
- same issues regarding race conditions inherent in the interface as for the stack
    - combine front() and pop() into a single function call
- when using a queue to pass data between threads, the receiving thread often needs to wait for the data
    - two variants on pop()
        - try_pop() - tries to pop the value from the queue but always returns immediately (with an indication of
          failure) even if there wasn't a value to retrieve
        - wait_and_pop() - wait until there's a value to retrieve
- Listing 4.3
- limit the constructors and eliminated assignment to simplify the code
    - two versions of both try_pop() and wait_for_pop()
        1. stores the retrieved value in the referenced variable, so it can use the return value for status
            - returns true if it retrieved a value and false otherwise
        2. returns the retrieved value directly
            - returned pointer can be set to NULL if there's no value to retrieve
- can extract the code for push() and wait_and_pop() from 4.1
- Listing 4.4
    - mutex and condition variable are now contained within the threadsafe_queue instance
        - separate variables are no longer required
        - no external synchronization is required for the call to push()
    - wait_and_pop() takes care of the condition variable wait
    - other overload of wait_and_pop() is now trivial to write
    - remaining functions can be copied almost verbatim from listing 3.5
- Listing 4.5
    - parameter to the copy constructor is a const reference
        - other threads
            - may have non-const references to the object
            - may call mutating member functions
    - empty() is a const member function
    - still need to lock the mutex
    - object must be marked mutable so it can be locked in empty() and in the copy constructor since locking a mutex
      is a mutating operation
- condition variables are also useful where there's more than one thread waiting for the same event
    - same structure as listing 4.1 can be used
        - if the threads are being used to divide the workload
        - only one thread should respond to a notification
    - just run multiple instances of the data—processing thread
    - when new data is ready, the call to notify_one() will trigger one of the threads currently executing wait()
      to check its condition and thus return from wait()
    - no guarantee which thread will be notified or even if there's a thread waiting to be notified
        - all the processing threads might be still processing data
    - several threads might be waiting for the same event and all of them need to respond
        - can happen when shared data is being initialized, and the processing threads can all use the same data
          but need to wait for it to be initialized
        - can happen when the threads need to wait for an update to shared data, such as a periodic reinitialization
    - thread preparing the data can call the notify_all() member function on the condition variable rather than
      notify_one() in these cases
        - causes all the threads currently executing wait() to check the condition they're waiting for
    - condition variable might not be the best choice of synchronization mechanisms if the waiting thread is going to
      wait only once
        - when the condition is true it will never wait on this condition variable again
        - especially true if the condition being waited for is the availability of a particular piece of data
    - future might be more appropriate

## 4.2 Waiting for one-off events with futures
- C++ Standard Library models one-off event with a future
    - if a thread needs to wait for a specific one-off event it obtains a future representing this event
    - thread can then periodically wait on the future for short periods of time to see if the event has occurred
      while performing some other task in between checks
    - thread can alternatively do another task until it needs the event to have happened before it can proceed and
      then just wait for the future to become ready
- future may have data associated with it
- future can't be reset once an event has happened (and future has become ready)
- two futures in the C++ Standard Library
    - implemented as class templates declared in the <future> library header
    - unique futures (std::future<>)
    - shared futures (std::shared_future<>)
    - modeled after std::unique_ptr and std::shared_ptr
- instance of std::future is the one and only instance that refers to its associated event
- multiple instances of std::shared_future may refer to the same event
    - all the instances will become ready at the same time, and they may all access any data associated with the event
- associated data is the reason these are templates
    - template parameter is the type of the associated data
- std:future<void>, std::shared_future<void> template specializations should be used when there's no associated data
- future objects themselves don't provide synchronized accesses even though futures are used to communicate between
  threads
- multiple threads must protect access via a mutex or other synchronization mechanism if they need to access a single
  future object
    - multiple threads may each access their own copy of a std::shared_future<> without further synchronization
        - even if they all refer to the same asynchronous result
- most basic one-off events is the result of a calculation that has been run in the background
    - std::thread doesn't provide an easy means of returning a value from such a task

### Returning values from background tasks
- for long-running calculation that will eventually yield a useful result but don't currently need the value
    - could start a new thread to perform the calculation
        - have to take care of transferring the result back
            - std::thread doesn't provide a direct mechanism for doing this
    - use std::async function template (in <future> header)

- use std::async to start an asynchronous task for which result is not needed right away
    - returns a std::future object
        - will eventually hold the return value of the function
    - call get() on the future when the value is needed
        - thread blocks until the future is ready and then returns the value
- Listing 4.6
- std::async permits passing additional arguments
    - second argument should provide the object on which to apply the member function if the first argument is a
      pointer to a member function
        - directly
        - via a pointer
        - wrapped in std::ref
    - remaining arguments are passed as arguments to the member function
    - copies are created by moving the originals if the arguments are rvalues
        - allows the use of move-only types as both the function object and the arguments
- Listing 4.7
- up to the implementation whether
    - std::async starts a new thread
    - the task runs synchronously when the future is waited for
- can specify which option to use with an additional parameter to std::async before the function to call
    - parameter is of the type std::launch
    - std::launch::deferred
        - indicates that the function call is to be deferred until either wait() or get() is called on the future
        - NOTE: means function may never actually run
    - std::launch::async
        - indicates that the function must be run on its own thread
    - std::launch::deferred | std::launch::async
        - indicates that the implementation may choose
        - default
```
auto f6 = std::async(std::launch::async,Y(),1.2); // runs in a new thread
auto f7 = std::async(std::launch::deferred,baz,std::ref(x)); // runs in wait() or get()
auto f8 = std::async(std::launch::deferred | std::launch::async, baz, std::ref(x)); // implementation chooses
auto f9 = std::async(baz,std::ref(x)); // implementation chooses
f7.wait(); // invokes the deferred function
```
- std::async makes it easy to divide algorithms into tasks that can be run concurrently
    - not the only way to associate a std::future with a task
        - can be done by wrapping the task in an instance of the std::packaged_task<> class template
        - can be done by writing code to explicitly set the values using the std::promise<> class template
- std::packaged_task is a higher-level abstraction than std::promise

### Associating a task with a future
- std::packaged_task<> ties a future to a function or callable object
    - when invoked, it calls the associated function or callable object and makes the future ready
        - return value stored as the associated data
    - can be used
        - as a building block for thread pools
        - to run each task on its own thread
        - to run all tasks sequentially on a particular background thread
        . If a large operation can be divided into
- each task in a large operation composed of self-contained sub-tasks can be wrapped in a std::packaged_task<> instance
    - instance can be passed to the task scheduler or thread pool
- template parameter for the std::packaged_task<> class template is a function signature
    - void() for a function taking no parameters with no return value
    - int(std::string&,double*) for a function that takes a non-const reference to a std::string and a pointer to
      a double and returns an int

- must pass in a function or callable object that can accept the specified parameters and that returns a type
  convertible to the specified return type when constructing an instance of std::packaged_task
    - types don't have to match exactly
        - can construct a std:: packaged_task<double(double)> from a function that takes an int and returns a float
            - types are implicitly convertible
- return type of the specified function signature identifies the type of the std::future<> returned from the
  get_future() member function
    - argument list of the function signature is used to specify the signature of the packaged task's function
      call operator
- Listing 4.8
- std::packaged_task object is a callable object
    - can be
        - wrapped in a std::function object
        - passed to a std::thread as the thread function
        - passed to another function that requires a callable object
        - even invoked directly
- when invoked as a function object
    - arguments supplied to the function call operator are passed on to the contained function
    - return value is stored as the asynchronous result in the std::future obtained from get_future()
- i.e. can wrap a task in a std::packaged_task and retrieve the future before passing the std::packaged_task object
  elsewhere to be invoked later
   - can wait for the future to become ready when the result is needed

#### Passing tasks between threads
- std::packaged_task provides a way of sending messages between threads
- Listing 4.9
- loop until a message has been received
    - repeatedly poll for messages to handle (events or tasks on a queue)
    - loop again if no tasks on the queue
    - otherwise
        - take task from queue
        - release the lock on the queue
        - run task
    - the future associated with the task will then be made ready when the task completes
- to post a task on the queue
    - create a new packaged task from the supplied function
    - obtain the future from that task by calling get_future()
    - put the task on the list before the future is returned to the caller
- the code that posted the original message can
    - wait for the future if it needs to know that the task has been completed
    - can discard the future if it doesn't need to know

### Making (std::) promises
- bad to use separate threads for operations that are going to increase to a very large number (e.g. handling
  connections)
    - large numbers of threads consequently consume large numbers of operating system resources and potentially
      cause a lot of context switching
    - operating system may run out of resources for running new threads before its capacity is exhausted (in extreme
      case)
    - case for a thread pool
        - results in other parts of the application waiting either for data to be successfully sent or received
- std::promise<T> provides a means of setting a value (of type T), which can later be read through an
  associated std::future<T> object
    - std::promise/std::future pair
        - waiting thread can block on the future
        - thread providing the data could use the promise half of the pairing to set the associated value and
          make the future ready
- can obtain the std::future object associated with a given std::promise by calling the get_future() member function
    - future becomes ready and can be used to retrieve the stored value when the value of the promise is set
      (using the set_value() member function)
    - an exception is stored instead if std::promise is destroyed without setting a value
- Listing 4.10
- need to handle exceptions
    - code could just report an error with an exception if performing the operation in the thread that needed the result
        - unnecessarily restrictive to require that everything go well to use a std::packaged_task or a std::promise

### Saving an exception for the future
```
double square_root(double x)
{
    if(x < 0) {
        throw std::out_of_range("x<0");
    }
    return sqrt(x);
}
```
- async call
```
std::future<double> f=std::async(square_root,-1);
double y = f.get();
```
- if the function call invoked as part of std::async throws an exception
    - exception is stored in the future in place of a stored value
    - future becomes ready
    - call to get() rethrows that stored exception
- NOTE: standard leaves it unspecified whether it is the original exception object that's rethrown or a copy
- same happens if function is wrapped in a std::packaged_task
    - if the wrapped function throws an exception when the task is invoked
        - exception is stored in the future in place of the result
        - call to get() rethrows that stored exception
- std::promise provides the same facility with an explicit function call
    - call the set_exception() member function rather than set_value() to store an exception rather than a value
    - typically used in a catch block for an thrown exception
```
extern std::promise<double> some_promise;
try {
    some_promise.set_value(calculate_value());
} catch(...) {
    some_promise.set_exception(std::current_exception());
}
```
- uses std::current_exception() to retrieve the thrown exception
    - alternative would be to use std::copy_exception() to store a new exception directly without throwing
```
some_promise.set_exception(std::copy_exception(std::logic_error("foo ")));
```
- much cleaner than using a try/catch block if the type of the exception is known
    - prefer this (simplifies and compiler can optimize)
- another way to store an exception in a future is to destroy the std::promise or std::packaged_task associated
  with the future without calling either of the set functions on the promise or invoking the packaged task
    - destructor of the std::promise or std::packaged_task will store a std::future_error exception with an error
      code of std::future_errc::broken_promise in the associated state if the future isn't already ready
    - make a promise to provide a value or exception by creating a future
    - promise is borken by destroying the source of that value or exception without providing one
        - waiting threads could potentially wait forever if the compiler didn't store anything in the future in this case
- std::future has limitations
    - only one thread can wait for the result
- use std::shared_future instead to wait for the same event from more than one thread

### Waiting from multiple threads
- calls to the member functions of a particular std::future instance are not synchronized with each other even though
  std::future handles all the synchronization necessary to transfer data from one thread to another
    - accessing a single std::future object from multiple threads without additional synchronization results in
      a data race and undefined behavior
    - std::future models unique ownership of the asynchronous result
    - one-shot nature of get() makes such concurrent access pointless
        - only one thread can retrieve the value
- std::shared_future instances are copyable
    - can have multiple objects referring to the same associated state
    - std::future is only moveable
        - ownership can be transferred between instances
        - only one instance refers to a particular asynchronous result at a time
- member functions of std::shared_future on an individual object are still unsynchronized
    - protect accesses with a lock to avoid data races when accessing a single object from multiple threads
- preferred way to use std::shared_future is to take a copy of the object
    - each thread can then access its own copy
    - accesses to shared asynchronous state from multiple threads are safe if each thread accesses that state through
      its own std::shared_future object
- potential use of std::shared_future
    - implementing parallel execution of large matrix or set of cells
        - each cell has a single final value
        - final value may be used for final values of other cells
        - formulas for calculating the results of the dependent cells can use a std::shared_future to reference the
          first cell
    - tasks that can proceed to completion will do so if all the formulas for the individual cells are then executed
      in parallel
        - those that depend on others will block until their dependencies are ready
        - will allow the system to make maximum use of the available hardware concurrency
- std::shared_future instances that reference some asynchronous state are constructed from instances of std::future
  that reference that state
    - std::future objects don't share ownership of the asynchronous state with any other object
    - ownership must be transferred into the std::shared_future using std::move
        - leaves std::future in an empty state
```
std::promise<int> p;
std::future<int> f(p.get_future());
assert(f.valid());
std::shared_future<int> sf(std::move(f));
assert(!f.valid());
assert(sf.valid());
```
- future f is initially valid because it refers to the asynchronous state of the promise
    - f is no longer valid after transferring the state to sf
        - sf is d
Just as with other movable objects, the
- transfer of ownership is implicit for rvalues
    - can construct a std::shared_future directly from the return value of the get_future() member function of a
      std::promise object
```
std::promise<std::string> p;
std::shared_future<std::string> sf(p.get_future()); // implicit transfer of ownership
```
- transfer of ownership is implicit
    - std::shared_future<> is constructed from an rvalue of type std::future<std::string>
    - std::future also has an additional feature to facilitate the use of std::shared_future with the new
      facility for automatically deducing the type of a variable from its initializer
    - std::future has a share() member function that creates a new std::shared_future and transfers ownership to it
      directly
```
std::promise< std::map< SomeIndexType, SomeDataType, SomeComparator, SomeAllocator>::iterator> p;
auto sf=p.get_future().share();
```
- type of sf is deduced to be
  `std::shared_future<std::map<SomeIndexType, SomeDataType, SomeComparator, SomeAllocator>::iterator>`
    - only need to change the type of the promise if the comparator or allocator is changed
        - type of the future is automatically updated to match
- may want to limit the amount of time of waiting for an event
    - may have a hard time limit
    - may have other useful work that the thread can be doing if the event isn't going to happen soon

## 4.3 Waiting with a time limit
- blocking calls will block indefinitely
    - suspend the thread until event occurs
- may want to limit time to wait
    - to send heartbeats
    - respond to user
    - to abort if requested/necessary
- two types of timeouts
    - duration-based timeout
        - wait for a specific amount of time 
    - absolute timeout
        - wait until a specific point in time 
- most of the waiting functions provide variants that handle both forms of timeouts
    - duration-based timeouts have a _for suffix
    - absolute timeouts have a _until suffix
- e.g. std::condition_variable 
    - two overloads of wait_for() 
    - two overloads of wait_until() 
    - one overload just waits until signaled, or the timeout expires, or a spurious wakeup occurs
    - other will check a supplied predicate when woken and will return only when the supplied predicate is true 
      or the timeout expires

### Clocks
- clock is a source of time information in C++ Standard
    - time now
    - type of the value used to represent the times obtained from the clock
    - tick period of the clock
    - whether or not the clock ticks at a uniform rate and is thus considered to be a
      steady clock
- current time of a clock can be obtained by calling the static member function now()
    - e.g. std::chrono::system_clock::now()
- type of the time points for a particular clock is specified by the time_point member typedef
    - return type of some_clock::now() is some_clock::time_point
- tick period of the clock is specified as a fractional number of seconds
    - given by the period member typedef of the clock
    - clock that ticks 25 times per second has a period of std::ratio<1,25>
    - clock that ticks every 2.5 seconds has a period of std::ratio<5,2>
    - period may be specified as the average tick period, smallest possible tick period, or some other value that the
      library writer deems appropriate if the tick period of a clock can't be known until runtime or may vary during a
      given run of the application
    - no guarantee that the observed tick period in a given run of the program matches the specified period for that
      clock
- clock is said to be a steady clock if it ticks at a uniform rate and can't be adjusted
    - (regardless of if rate matches the period)
    - is_steady static data member of the clock class is true if the clock is steady
    - std::chrono::system_clock will not be steady, because the clock can be adjusted, even if such adjustment is done
      automatically to take account of local clock drift
        - clock adjustment may cause a call to now() to return a value earlier than that returned by a prior call
          to now()
        - violates requirement for a uniform tick rate
    - steady clocks are important for timeout calculations
        - std::chrono::steady_clock
- other clocks
    - std::chrono::system_clock
        - represents the "real time" clock of the system
        - provides functions for converting its time points to and from time_t values
     - std::chrono::high_resolution_clock
        - provides smallest possible tick period (highest possible resolution) of all the
          library-supplied clocks
        - may actually be a typedef to one of the other clocks
- defined in the <chrono> library header

### Durations
- simplest part of the time support
    - std:: chrono::duration<> class template
    - first template parameter is the type of the representation (such as int, long, or double)
    - second is a fraction specifying how many seconds each unit of the duration represents
        - e.g. a number of minutes stored in a short is std::chrono::duration<short,std:: ratio<60,1>>
            - 60 seconds in a minute
        - e.g. count of milliseconds stored in a double is std::chrono::duration<double,std::ratio <1,1000>>
            - millisecond is 1/1000 of a second
- all the C++ time-handling facilities used by the Thread Library are in the std::chrono namespace
- predefined typedefs in the std::chrono namespace for various durations: nanoseconds, microseconds, milliseconds,
  seconds, minutes, and hours
    - all use a sufficiently large integral type for the representation chosen such that a
      duration of over 500 years in the appropriate units can be represented
- also typedefs for all the SI ratios from std::atto (10–18) to std::exa (1018)
    - (more, if platform has 128-bit integer types)
    - use when specifying custom durations such as std::duration<double,std::centi> for a count of 1/100 of a second
      represented in a double
- conversion between durations is implicit where it does not require truncation of the value
    - converting hours to seconds is OK
    - converting seconds to hours is not
- explicit conversions can be done with std::chrono::duration_cast<>
```
std::chrono::milliseconds ms(54802);
std::chrono::seconds s = std::chrono::duration_cast<std::chrono::seconds>(ms);
```
- result is truncated rather than rounded
    - s will have a value of 54
- durations support arithmetic
    - can add and subtract durations to get new durations
    - multiply or divide by a constant of the underlying representation type (the first template parameter)
    - e.g. `5 * seconds(1)` same as `seconds(5)` or `minutes(1) – seconds(55)`
- count of the number of units in the duration can be obtained with the count() member function
    - `std::chrono::milliseconds(1234).count()` is 1234
- duration-based waits are done with instances of std::chrono::duration<>
    - can wait for up to 35 milliseconds for a future to be ready
```
std::future<int> f=std::async(some_task);
if (f.wait_for(std::chrono::milliseconds(35)) == std::future_status::ready) {
    do_something_with(f.get();
}
```
- wait functions all return a status to indicate whether the wait timed out or the waitedfor event occurred
    - waiting for a future
        - returns std::future_status::timeout if the wait times out
        - returns std::future_status::ready if the future is ready
        - returns std::future_status::deferred if the future's task is deferred
- time for a duration-based wait is measured using a steady clock internal to the library
    - 35 milliseconds means 35 milliseconds of elapsed time
        - even if the system clock was adjusted (forward or back) during the wait
    - vagaries of system scheduling and the varying precisions of OS clocks means that the actual time between the
      thread issuing the call and returning from it may be much longer than 35 ms

### Time points
- time point for a clock is represented by an instance of the std::chrono::time_point<> class template
    - specifies which clock it refers to as the first template parameter and the units of measurement
      (a specialization of std::chrono::duration<>) as the second template parameter
    - value of a time point is the length of time (in multiples of the specified duration) since a specific point in
      time called the epoch of the clock
- epoch of a clock is a basic property but not something that's directly available to query or specified by
  the C++ Standard
- typical epochs include 00:00 on January 1, 1970 and the instant when the computer running the application booted
  up
- clocks may share an epoch or have independent epochs
    - time_point typedef in one class may specify the other as the clock type associated with the time_point if two
      clocks share an epoch
    - can get the time_since_epoch() for a given time_point but can't find out when the epoch is
        - returns a duration value specifying the length of time since the clock epoch to that particular time point
- e.g. `std::chrono::time_point<std::chrono::system_clock, std::chrono::minutes>`
    - holds the time relative to the system clock but measured in minutes as opposed to the native precision of
      the system clock (which is typically seconds or less)
- can add durations and subtract durations from instances of std::chrono::time_point<> to produce new time points
    - `std::chrono::high_resolution_clock::now() + std::chrono::nanoseconds(500)`
        - returns a time 500 nanoseconds in the future
    - good for calculating an absolute timeout when the maximum duration of a block of code is known
    - multiple calls to waiting functions within it or nonwaiting functions that precede a waiting function but
      take up some of the time budget
- can also subtract one time point from another that shares the same clock
    - result is a duration specifying the length of time between the two time points
    - useful for timing blocks of code
```
auto start = std::chrono::high_resolution_clock::now();
do_something();
auto stop = std::chrono::high_resolution_clock::now();
std::cout << "do_something() took " << std::chrono::duration<double,std::chrono::seconds>(stop-start).count()
          << " seconds\n";
```
- clock parameter of a std::chrono::time_point<> instance does more than just specify the epoch
- when passing the time point to a wait function that takes an absolute timeout, the clock parameter of the time
  point is used to measure the time
- has important consequences when the clock is changed
    - wait tracks the clock change and won't return until the clock's now() function returns a value later than the
      specified timeout
    - may reduce the total length of the wait (as measured by a steady clock) if the clock is adjusted forward
    - may increase the total length of the wait if it's adjusted backward
- time points are used with the _until variants of the wait functions
    - typical use case is as an offset from some-clock::now() at a fixed point in the program
    - time points associated with the system clock can be obtained by converting from a time_t using the
      std::chrono::system_clock::to_time_point() static member function for scheduling operations at a user-visible
      time
- Listing 4.11
- recommended way to wait for condition variables with a time limit
    - if not passing a predicate to the wait
    - overall length of the loop is bounded
    - need to loop when using condition variables if the predicate isn't passed in to handle spurious wakeups
    - might end up waiting almost the full length of time before a spurious wake, and the next time through the
      wait time starts again if using wait_for()
        - may repeat any number of times, making the total wait time unbounded

### Functions that accept timeouts
- add a delay to the processing of a particular thread
    - doesn't take processing time away from other threads when it has nothing to do
    - two functions that handle this are std::this_thread::sleep_for() and std::this_thread::sleep_until()
    - work like a basic alarm clock
        - thread goes to sleep either for the specified duration (with sleep_for()) or until the specified point in
          time (with sleep_until())
        - sleep_for() makes sense for cases where something must be done periodically and the elapsed time is what
          matters
        - sleep_until() allows for scheduling the thread to wake at a particular point in time
- sleeping isn't the only facility that takes a timeout
    - can use timeouts with condition variables and futures
    - can use timeouts when trying to acquire a lock on a mutex if the mutex supports it
- std::mutex and std::recursive_mutex don't support timeouts on locking
- use std::timed_mutex or std::recursive_timed_mutex
    - support try_lock_for() and try_lock_until() member functions that try to obtain the lock within a specified time
      period or before a specified time point
- Table 4.1

## 4.4 Using synchronization of operations to simplify code
- synchronization facilities
    - allow a more functional (in the sense of functional programming) approach to programming concurrency
        - each task can be provided with the data it needs rather than sharing data directly between threads
        - result can be passed to any other threads that need it through the use of futures

### Functional programming with futures
- focus on idea that if function a function is invoked twice with the same parameters, the result is exactly the same
- pure function doesn't modify any external state
- simplifies reasoning especially when concurrency is involved
    - removes shared memory == no race conditions and no need to protect shared data with mutexes either
- when most functions are pure impure functions that actually do modify the shared state stand out more
    - easier to reason about how they fit into the overall structure of the application
- C++ is a multiparadigm language
    - possible to write programs in a functional style
        - lambda functions
        - std::bind from Boost and TR1
        - automatic type deduction for variables
        - futures
            - future can be passed around between threads to allow the result of one computation to depend on the
              result of another without any explicit access to shared data

#### FP-style quicksort
To illustrate the use of futures for FP-style concurrency, let's look at a simple imple- mentation of the
Quicksort algorithm. The basic idea of the algorithm is simple:
- given a list of values
    - take an element to be the pivot element
    - partition the list into two sets
        - less than the pivot
        - greater than or equal to the pivot
     - sorted copy of the list is obtained by sorting the two sets and returning the sorted list of values less
       than the pivot, followed by the pivot, followed by the sorted list of values greater than or equal to the pivot
- FP-style sequential implementation is shown
    - takes and returns a list by value rather than sorting in place like std::sort() does
- Listing 4.12
- interface is FP-style
    - using FP-style throughout leads to a lot of copying
    - use imperative style for the internals
    - take the first element as the pivot by slicing it off the front of the list using splice()
        - can potentially result in a suboptimal sort (in terms of numbers of comparisons and exchanges)
        - doing anything with a std::list can be time consuming because of the list traversal
    - splice it directly into the list because it is in result
    - also use it for comparisons
        - take a reference to it to avoid copying
    - then use std::partition to divide the sequence into those values less than the pivot and those not less than
      the pivot
        - easiest way to specify the partition criteria is to use a lambda function
        - use a reference capture to avoid copying the pivot value
        - std::partition() rearranges the list in place and returns an iterator marking the first element
          that's not less than the pivot value
    - if using recursion to sort the two "halves" need to create two lists
        - can do this by using splice() again to move the values from input up to the divide_point into a new list
        - leaves the remaining values alone in input
    - then sort the two lists with recursive calls
        - use std::move() to pass the lists in
        - can avoid copying by using std::move9) to pass the lists in
    - can use splice() again to piece the result together in the right order

#### FP-style parallel quicksort
- same operations as before except that some of them now run in parallel
- Listing 4.13
- sort lower portion on another thread using std::async() rather than current thread
- upper portion of the list is sorted with direct recursion as before
- can take advantage of the available hardware concurrency by recursively calling parallel_quick_sort()
    - recursing down three times if std::async() starts a new thread every time leads to 8 running threads
    - recurse down 10 times (for ~1000 elements) leads to 1024 threads running if the hardware can handle it
        - if the library decides there are too many spawned tasks (perhaps because the number of tasks has exceeded
          the available hardware concurrency), it may switch to spawning the new tasks synchronously
        - will run in the thread that calls get() rather than on a new thread
            - avoids the overhead of passing the task to another thread when this won't help the performance
    - NOTE: conformant to standard for an implementation of std::async to start a new thread for each task
      (even in the face of massive oversubscription) unless std::launch::deferred is explicitly specified or to run
      all tasks synchronously unless std::launch::async is explicitly specified
        - when relying on the library for automatic scaling best to check the documentation for current implementation
          to see what behavior it exhibits
- can also write custom spawn_task() function as a simple wrapper around std::packaged_task and std::thread rather
  than using std::async()
    - create a std::packaged_task for the result of the function call
    - get the future from it
    - run it on a thread
    - return the future
    - doesn't offer much advantage and would likely lead to massive oversubcription
    - sets up abilitiy to migrate to a more sophisticated implementation that adds the task to a queue to be run
      by a pool of worker threads
    - worth it over using std::async only if programmer really knows what they're doing and want complete control
      over the way the thread pool is built and executes tasks
- can just splice new_higher because of direct recursion to get new_higher
    - new_lower is now a std::future<std::list<T>> rather than just a list
        - need to call get() to retrieve the value before splice() can be called
        - waits for the background task to complete and moves the result into the splice() call
            - get() returns an rvalue reference to the contained result
            - can be moved out
- still isn't an ideal parallel implementation of Quicksort even assuming that std::async() makes optimal use of the
  available hardware concurrency
    - std::partition does a lot of the work but is still sequential
    - check the academic literature for fastest possible parallel implementation
- Listing 4.14
- another paradigm is CSP (Communicating Sequential Processes) where threads are conceptually entirely separate
    - no shared data but with communication channels that allow messages to be passed between them
    - Erlang (http://www.erlang.org/) and by the MPI (Message Passing Interface) (http://www.mpi-forum.org/)
      environment commonly used for high-performance computing in C and C++

### Synchronizing operations with message passing
- idea of CSP
    - no shared data
    - each thread can be reasoned about entirely independently
    - consider how it behaves in response to the messages that it received
- each thread is a state machine
    - when it receives a message, it updates its state in some manner and maybe sends one or more messages
      to other threads, with the processing performed depending on the initial state
- can formalize this and implement a Finite State Machine model
- state machine can be implicit in the structure of the application
- separation into independent processes has the potential to remove much of the complication from shared-data
  concurrency and therefore make programming easier
- true communicating sequential processes have no shared data
    - all communication passed through message queues
    - not possible to enforce this requirement because C++ threads share an address space
- requires discipline as application or library authors
    - ensure that we don't share data between the threads
- message queues must be shared in order for the threads to communicate
    - details can be wrapped in the library
- state machines
    - in each state the thread waits for an acceptable message, which it then processes
    - may result in transitioning to a new state, and the cycle continues
    - can implement collected states with a class that has a member function to represent each state
    - each member function can then wait for specific sets of incoming messages and handle them when they arrive,
      possibly triggering a switch to another state
    - each distinct message type is represented by a separate struct
- all the necessary synchronization for the message passing is entirely hidden inside the message-passing library
  (see appendix C)
- Listing 4.15
- implementation described here is simplified from the real logic
    - no need to think about synchronization and concurrency issues, just which messages may be received at any given
      point and which messages to send
    - state machine runs on a single thread
    - other parts of the system run on separate threads
- style of program design is called the Actor model
    - several discrete actors in the system (each running on a separate thread)
    - send messages to each other to perform the task at hand
    - no shared state except that directly passed via messages
- NOTE: CSP is distinct from the Actor model in that it formalizes and abstracts the message channels
- Listing 4.16
- simplifies the task of designing a concurrent system
    - each thread can be treated entirely independently
- example of using multiple threads to separate concerns and as such requires explicitly deciding how to divide
  the tasks between threads

## 4.5 Summary
- synchronizing operations between threads is an important part of writing an application that uses concurrency
    - no synchronization = threads are essentially independent and might as well be written as separate applications
      that are run as a group because of their related activities
- methods for synchronization
    - basic condition variables
    - futures
    - promises
    - packaged tasks
- ways of approaching synchronization issues
    - functional-style programming
        - each task produces a result entirely dependent on its input rather than on the external environment
    - message passing
        - communication between threads is via asynchronous messages sent through a messaging subsystem that acts as
          an intermediary
