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
  the same time, much as you can run your web browser and word processor at the same time
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
    - hard to manage because memory addresses of the same data aren’t necessarily the same in
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
- because you cannot return a value from a thread, must pass in a reference to return a result
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
- if all shared data is read-only, there’s no problem, because the data read by one thread is unaffected
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
- simplest option is to wrap your data structure with a protection mechanism, to ensure that only the thread actually
  performing a modification can see the intermediate states where the invariants are broken
- lock-free programming - modify the design of data structure and its invariants so that modifications
  are done as a series of indivisible changes, each of which preserves the invariants
- software transactional memory (STM) - can handle the updates to the data structure as a transaction
    - required series of data modifications and reads is stored in a transaction log and then committed
      in a single step
    - if the commit can’t proceed because the data structure has been modified by another
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
- also important to check that member functions don’t pass such pointers or references in to
  functions they call that aren’t under application programmer's control
    - those functions might store the pointer or reference in a place where it can later be
      used without the protection of the mutex
    - particularly dangerous in this regard are functions that are supplied at runtime via a function argument or
      other means
- general guideline
    - don’t pass pointers and references to protected data outside the scope of the lock
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
- pass a reference to a variable in which you wish to receive the popped value as an argument in the call to pop()
    - requires construction of stack's value type prior to call
    - may not be possible for certain types
    - requires that the stored type is assignable

#### Option 2: Require a no-throw copy constructor or move constructor

#### Option 3: Return a pointer to the popped item

#### Option 4: Provide both option 1 and either option 2 or 3

### Example definition of a thread-safe stack
- Listing 3.5