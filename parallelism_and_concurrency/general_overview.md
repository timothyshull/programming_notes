```
Concurrency is about dealing with lots of things at once.
Parallelism is about doing lots of things at once.
Not the same, but related.
One is about structure, one is about execution.
Concurrency provides a way to structure a solution to solve a problem that
may (but not necessarily) be parallelizable.
Rob Pike
```

# Parallel Libs/Frameworks
- Cilk Plus -> install with custom gcc branch or custom llvm branch
    - https://gcc.gnu.org/git/gcc.git/
    - https://cilkplus.github.io/
- Threading Building Blocks - brew install tbb
- OpenMP - brew install gcc —-without-multilib
- CUDA - download installer from nvidia
- OpenCL - https://developer.apple.com/opencl/
    - included on mac OS after 10.7, integrates with GCD
    - https://developer.apple.com/library/content/documentation/Performance/Conceptual/OpenCL_MacProgGuide/Introduction/Introduction.html
- OpenGL - https://developer.apple.com/opengl/
- HPX - https://github.com/STEllAR-GROUP/hpx
- macOS Frameworks
    - Accelerate
    - Metal
    - OpenCL
    - OpenGL

    - NetFS
    - GLUT
    - GlKit


# Additional Resources
- http://parlab.eecs.berkeley.edu/wiki/patterns/patterns
- https://bitbucket.org/VictorEijkhout/hpc-book-and-course
- https://bitbucket.org/VictorEijkhout/parallel-computing-book

# Patterns for Parallel Programming
## Finding Concurrency
### Task Decomposition
### Data Decomposition
### Group Tasks
### Order Tasks
### Data Sharing
### Design Evaluation

## Algorithm Structure
### Task Parallelism
### Divide and Conquer
### Geometric Decomposition
### Recursive Data
### Pipeline
### Event-Based Coordination

## Supporting Structures
### SPMD
### Master/Worker
### Loop Parallelism
### Fork/Join
### Shared Data
### Shared Queue
### Distributed Array
### Other
- SIMD
- MPMD
- Client-Server Computing
- Concurrent Programming with Declarative Languages

## Implementation Mechanisms
### UE Management
### Thread Creation/Destruction
### Process Creation/Destruction
### Memory Synchronization and Fences
### Barriers
### Mutual Exclusion
### Message Passing
### Collective Communication
### Other Communication Constructs

## Structured Parallel Programming
### Models
- Cilk Plus
- Threading Building Blocks
- OpenMP
- Array Building Blocks (ArBB)

### Classification
- Flynn's Taxonomy
    - SISD
    - SIMD
    - MIMD
- shared memory
- distributed memory
- SIMT (Single Instruction, Multiple Threads)
### Map
### Collectives
### Data Reorganization
### Stencil and Recurrence
### Fork-Join
### Pipeline

# Pattern-Oriented Software Architecture, Patterns for Concurrent and Networked Objects: 2
## Reactor pattern
## Proactor pattern
## Asynchronous Completion Token
## Acceptor-Connector
- http://www.cs.wustl.edu/~schmidt/POSA/POSA2/event-patterns.html


# Python

## Coroutines
### Consider Coroutines to run many functions concurrently
- problems with threads
    - require special tools for safe coordination which makes them more difficult
      to reason about, difficult to extend, and harder to maintain
    - require a lot of memory (~8MB per thread)
    - costly to start
- coroutines are implemented as an extension to generators and cost a function call
  to start (functions have low overhead in python)
- allow code consuming generator to pass values back into the generator with each
  call using the `.send()` method
- initiate with a call to next
- `yield from` expression allows composition of generator coroutines, facilitating
  composition of larger coroutines from smaller ones
- Python 2 requires extra work and has additional limitations
- powerful tool for separating core logic of program from interaction with surrounding
  environment

## Generators
- coroutines via advanced generators (PEP 342) added in 2.5 which augments
  generators to have .send() method
- augmentation is how coroutines were born
    - generators produce data for iteration
    - coroutines are consumers of data
    - don't mix the two concepts (avoids complex reasoning)
    - coroutines are related to iteration
    - use of using yield to produce a value in a coroutine but not tied to
      iteration

## concurrent.futures
- use for true parallelism
- multiprocessing accessed via concurrent.futures
- runs additional interpreters as child processes to utilize multiple CPU cores
- each has separate GIL and can fully utilize one CPU core
- maintains link to parent, receives instructions and returns results
- ThreadPoolExecutor still bound by GIL
```
pool = ThreadPoolExecutor(max_workers=2)
results = list(pool.map(work, values))
```
- ProcessPoolExecutor will speed up considerably
```
pool = ProcessPoolExecutor(max_workers=2)
results = list(pool.map(work, values))
```
- underlying actions from multiprocessing of ProcessPoolExecutor
    1. reads each item from the input collection passed to map
    2. serializes data using pickle
    3. copies data from parent to child via a local socket
    4. child deserializes
    5. child imports work function and runs (parallel to other child processes)
    6. serializes result to bytes, transfers back to parent
    7. deserializes in parent and merges results into a single list
- well suited to small, isolated functions with no state sharing and small amounts
  of data passed from parent to children
- access through concurrent.futures and leave remaining multiprocessing components
  unless prepared to deal with complexity


## socket

## select

## epoll

## threading
### Use threads for blocking I/O and not parallelism
- CPython compiles source to byte(word)code and then interprets in a stack-based
  interpreter
- interpreter maintains state during a run, ensures coherence using GIL
- GIL is a mutex lock that prevents preemptive multithreading
    - thread taking control by interrupting another thread
- result of GIL is that multiple threads still means only one progresses at a time
- multiple threads can actually take more time in Python
- still useful to allow multiple functions to make progress without manually juggling
  because GIL ensures a certain amount of fairness between threads
- threads also help with blocking I/O by protecting program from hanging while
  waiting for system calls that result in blocking I/O
```
thread = Thread(target=slow_systemcall)
thread.start()
```
- Python threads release the GIL just before they make system calls and reacquire
  them as soon as they're done which means the system calls can run in parallel


### Use Lock to prevent data races in threads
- still need to lock data access between threads despite GIL because interpreter
  instructions can be interrupted mid-data access
- use the tools in the threading built-in module
    - Lock is the standard mutual-exclusion lock
```
lock = Lock()
with lock:
    # access data
```


### Use Queue to coordinate work between threads
- useful arrangement of concurrent work is pipeline of functions
- good for work that includes blocking I/O or subprocesses
    - easily parallelizable activities in Python
- with producer/consumer pipeline and custom queue/lock combo, varying speeds
  of worker functions can cause back up
- Queue class in queue built-in blocks on calls to get until new data is available
- Queue class allows specification of maximum amount of pending work allowed
  between two phases
- can track progress of work using task_done, eliminates need for polling
- issues with concurrent pipelines
    - busy waiting
    - stopping workers
    - memory explosion
- Queue has required facilities for building concurrent pipelines
    - blocking operations
    - buffer sizes
    - joining

## asyncio
- added in 3.4, built on coroutines
    - must use `yield from`
    - must invoke with `yield from` or pass to asyncio function
    - should use @asyncio.coroutine decorator
- Trollius backports to 2.6 with specific changes
- don't use time.sleep() in aysncio coroutines unless want to block the main
  thread
```
yield from asyncio.sleep(DELAY)
```
- differences between threads and aysncio
    - asyncio.Task ~= threading.Thread
    - Task is like a green thread in libraries that implement cooperative multitasking
      (gevent)
    - Task runs a coroutine and a Thread invokes a callable
    - don't manually create Tasks
        - coroutine -> asyncio.async()
        - coroutine -> loop.create_task()
    - Tasks are already scheduled to run
    - Threads must be `start`ed
    - threads work with standard functions, asyncio works with coroutines using
      `yield from`
    - threads do not have an API to cancel, tasks have Task.cancel() which raises
      CancelledError inside coroutine
    - outer coroutine must be executed with loop.run_until_complete in the main function
- coroutines in asyncio are protected against interruption by default
- must explicitly yield to allow rest of program to run (synchronized by definition)

### asyncio.Future
- similar interface to concurrent.futures.Future but not interchangeable
- BaseEventLoop.create_task() takes coroutine and schedules to run
- Task is subclass of Future, designed to wrap a coroutine
- analogous to concurrent Futures (created through Executor.submit())
- methods
    - done
    - add_done_callback
    - result
- designed to work with yield from
    - don't need .add_done_callback() -> put processing after yield from
    - don't need .result() -> result of a yield from expression is the result
```
result = yield from future
```

- TODO: more here

## subprocess
### Use subprocess to Manage Child Processes
- good for gluing command line utilities together
- Python is CPU bound but spinning up subprocesses allows for CPU-intensive workloads
- subprocess replaces
    - popen
    - popen2
    - os.exec*
```
proc = subprocess.Popen(['cmd'], stdout=subprocess.PIPE)
out, err = proc.communicate()
# out.decode('utf-8')

proc = subprocess.Popen(['cmd'])
while proc.poll() is None:
    # do something
proc.poll() # 0
```
- run in parallel with Python interpreter
- can write to child process stdin
- use timeout parameter with communicate to avoid deadlocks
  and hanging child processes

## C extensions
- can be used to implement performance-critical code without thread limitations
  of GIL (also runs faster)
- adds cost of time and comprehensibility


# Unix
## POSIX Threads

## Thread Synchronization

## Critical Sections and Semaphores

## POSIX IPC

## Asynchronous Events
### Signals
### Timers

## Communication
### Connection-Oriented Communication
- The Client-Server Model
- Communication Channels
- Connection-Oriented Server Strategies
- Universal Internet Communication Interface (UICI)
- UICI Implementations of Different Server Strategies Section
- UICI Clients
- Socket Implementation of UICI
- Host Names and IP Addresses
- Thread-Safe UICI
### Connectionless Communication and Multicast
- Simple-Request Protocols
- Request-Reply Protocols
- Request-Reply-Acknowledge Protocols
- UDP vs TCP
- Multicast

## Alternative I/O Models (basis for other mechanisms)
- Level-triggered
- Edge-Triggered

### I/O Multiplexing
- select
- poll

### Signal-Driven I/O
- SIGIO and signal handlers

### epoll (Linux)
- kqueue

### Waiting on Signals and File Descriptors
- pselect
- the self-pipe trick


# C++
## Threads

## Synchronization

## Futures

## Promises

## Atomics

## Custom thread pooling

## Message passing frameworks
- ZeroMQ



# The Art of Multiprocessor Programming
## Mutual Exclusion
## Concurrent Objects
## Foundations of Shared Memory
## Primitive Synchronization Operations
## Consensus
## Spin Locks and Contention
## Monitors and Blocking Synchronization
## Linked Lists
## Concurrent Queues and the ABA Problem
## Concurrent Stacks and Elimination
## Counting, Sorting and Distributed Coordination
## Concurrent Hashing and Natural Parallelism
## Skiplists and Balanced Search
## Priority Queues
## Futures, Scheduling, and Work Distribution
## Barriers
## Transactional Memory


# macOS
## Unix Sockets and Networking
## CFRunLoop, CFSocket and Events
## kqueue and FSEvents
## Bonjour (Zeroconf standard)
## Multiprocessing
- scheduling
- fork
- exec
- Pipes
## NSTask
### NSProcessInfo
### NSTask
### NSFileHandle
### NSPipe
## Multithreading
### Posix Threads
### Cocoa and Threading
- NSThread
## Operations
### NSOperationQueue
## Grand Central Dispatch
### Queues
### Dispatching
### Memory management


# SCMSW
- concurrency - dealing with lots of things at once
- parallelism - doing lots of things at once
## Threads and Locks
### Mutual Exclusion and Memory Models
- creating a thread
```
Thread t = new Thread() {
    public void run() {
        // do something
    }
};

t.start();
// Thread.yield();
// t.join():
```
- yield is a hint to the system scheduler that the thread is willing to yield its
  use of a system processor
- not calling yield after creating a thread adds startup overhead
- read-modify-write pattern is common
- intrinsic lock ~= mutex ~= monitor ~= critical section
- intrinsic locking
```
// synchronized method
public synchronized void increment() { ++count; }

// synchronized block
synchronized (var) {
    // do something with var
}
```
- race conditions are primary concern -> results in non-deterministic behavior
- remember
    - compiler optimizations can reorder instructions
    - JVM optimizations can reorder instructions
    - hardware optimizations can reorder instructions
- memory visibility - the visibility of memory changes between threads is defined by the
  Java memory model
    - no guarantees unless using synchronization in BOTH threads
- to avoid the possibility of deadlock, always lock multiple locks in a globally defined
  and consistent order
- do not use Java's hashCode method for ordering locks because it is not guaranteed to be
  unique
- deadlock - occurs when a process or thread enters a waiting state because a requested
  system resource is held by another waiting process, which in turn is waiting for another
  resource held by another waiting process
- summary:
    - synchronize all access to shared variables
    - use synchronization in both the writing and reading threads
    - acquire multiple locks in a fixed global order
    - don't call alien (unknown code) methods while holding a lock
    - hold locks for the shortest possible amount of time
- memory model
- double checked locking anti-pattern
- limits of intrinsic locks
    - no way to interrupt a blocked thread
    - no way to time out while trying to acquire an intrinsic lock
    - can only acquire with a synchronized block/method (lock acquisition and release must
      occur in the same method and must be strictly nested)
- ReentrantLock is the solution
    - explicit locking
    - interruptible locking
    - timeouts
- the only way for a thread to exit in Java is for the run method to complete, so a deadlocked
  thread cannot be interrupted
```
final l = new ReentrantLock();

Thread t = new Thread() {
    public void run() {
        try {
            l.lockInterruptibly();
        } catch (InterruptedException e) {}
    }
}
```
- timeouts
```
if (l.tryLock(1000, TimeUnit.MILLISECONDS)) {
    // lock acquired
} else {
    // unable to lock
}
```
- livelock - similar to deadlock, except that the states of the processes involved
  constantly change with regard to one another with neither progressing
- livelock is a risk with some algorithms that detect and recover from deadlock
    - if more than one process takes action, the deadlock detection algorithm can be
      repeatedly triggered
    - can be avoided by ensuring that only one process (chosen arbitrarily or by priority)
      takes action
- deadlock - a situation in which two or more processes are unable to proceed because each is waiting for one of the
  others to perform some action
- livelock - a situation in which two or more processes continuously change their states in response to changes in the
  other process(es) without doing any useful work
- starvation - a situation in which a runnable process is overlooked indefinitely by the scheduler; although it is
  able to proceed, it is never chosen
- hand-over-hand locking - used for sequenced containers (esp. linked list)
    - don't lock full container
    - iterate through container, locking nodes/elements on either side of the target element
      before locking and accessing the target element, release locks and move forward
- condition variables - synchronization primitive that is associated with a lock and used to signal to other threads
  the state of that desired condition
- a condition variable is associated with a lock and a thread must hold that lock before being able to wait on that
  condition
    - after the lock is acquired, the condition is checked and the thread either progresses or waits on the condition
- atomic operation - any operation that, from the point of view of another thread, appears to be a single operation
  (though it may be many collected into one) e.g. it can never be interrupted when partially complete
```
//
condition.signal()
//
l.lock();
try {
    while (condition) {
        condition.await();
    }
} finally {
    l.unlock();
}
```
- Java has atomics which provide sets of atomic operations on various types (generally primitives) that do not require
  locks
- atomics are the foundation of lock-free and non-blocking algorithms
- the volatile keyword in Java (unlike C++) ensures that reads and writes to that variable will not be reordered
- summary:
    - use ReentrantLock and java.util.concurrent.atomic to allow a thread to
        - be interrupted while trying to acquire a lock
        - time out while attempting to acquire a lock
        - acquire and release locks in any order
        - use condition variables to wait for arbitrary conditions
        - avoid locks entirely by using atomic variables

- use Executors.newFixedThreadPool(size) to create an ExecutorService thread pool and automatically queue excess
  thread creation requests, thus reducing risks of issues like DOS
- create thread pools the size of the number of CPUs on a machine or 2x for hyperthreaded
- copy-on-write - paradigm that makes a copy of a data structure whenever it is changed
    - don't forget this will invalidate iterators
    - only appropriate for certain use cases
    - more efficient than making a defensive copy each time
- for certain use cases a blocking queue is better than a non-blocking unbounded Queue
- summary:
    - use thread pools instead of creating threads directly
    - for listener management, consider copy-on-write data structures
    - use blocking queues for the producer-consumer pattern to allow producers to get ahead of consumers but limit
      this amount
    - use concurrent hash maps for concurrent access to maps
- questions:
    - how does a ForkJoinPool differ from a thread pool?
    - what is work stealing and when is it useful?
    - difference between CountDownLatch and CyclicBarrier?
    - Amdahl's Law?
- overview:
    - strengths
        - broad applicability (coarse to fine grain granularity of solutions for problems)
        - close to the metal and thus generally efficient
        - easily integrated into many different languages with different paradigms
    - weaknesses
        - no direct support for parallelism (can be used to parallelize a sequential algorithm, but solution has
          to be constructed from concurrent primitives, which can result in non-determinism)
        - generally support only shared memory architectures (need an alternate solution for distributed memory)
        - difficult to program with threads and locks
        - the difficulty generally lies in reasoning about what is happening and, more seriously, testing

# Functional Programming
- Clojure - dialect of Lisp that runs on the JVM
- functional programming is partially based on an (sometimes complete) avoidance of (shared) mutable state (be aware of
  pure vs. impure functional languages and the relationship to the distinction of functions w/wout side effects)
- removing shared mutable state makes concurrency much easier (to write, test and reason about)
- use fold from clojure.core.reducers for a parallel version of reduce
- lazy sequences - elements are generated only when they are needed
- summary:
    - avoid shared mutable state to simplify concurrency
    - map & mapcat - apply a function to every element of a sequence
    - laziness allows large or infinite sequence manipulation, only evaluating elements when needed
    - use reduce (or parallel fold) to reduce a sequence to a single value
- pmap is like map but parallel and semi-lazy, i.e. parallel computation stays ahead of consumption but it will
  not the entire result unless it is required
- reducer - recipe for how to reduce a collection
    - map - function and (possibly lazy) sequence -> (possibly lazy) sequence
    - reducer returns the recipe for creating the result
        - more efficient than a chain of functions returning lazy sequences because no intermediate need to be created
        - allows fold to parallelize the entire chain of operations on the underlying collection
- Clojure has protocols which are similar to Java's interfaces
- binary chop
- fold tree
- summary:
    - use pmap to parallelize map and return a semi-lazy parallel map
    - parallel map can be batched for efficiency with partition-all
    - fold parallelizes reduce oprations with an eager divide-and-conquer strategy
    - clojure.core.reducers versions of map, mapcat, and filter returns reducibles which can be thought of as recipes
      for how to reduce a sequence

- functional programming focuses on expressions and thus allows manipulation of the order of evaluation
    - much more declarative, statement of what result should be and method for evaluation is left to engine
- referential transparency - anywhere an invocation of the functions appears it can be replaced with result without
  changing the behavior of the program
- future - body of code that executes in another thread and returns a future object
- promise - value that is realized in a separate thread and blocks until it is realized
    - does not cause code to be run, but value is set with deliver
- summary:
    - functions in a functional language are referentially transparent
    - expression evaluation order can be safely modified
    - facilitates dataflow style of functional programming -> code executes when data it depends on becomes
      unavailable
- overview:
    - some concurrent programs always imply non-determinism, but not all parallel programs imply non-determinism
    - most race conditions that occur in traditional thread and lock-based programming occur because of the particular
      implementation of the solution rather than an intrinsic non-determinism in the problem
    - referential transparency allows for modification of order of evaluation and thus parallelization in an almost
      trivially easy way
    - monads and monoids are used in statically typed functional languages like Haskell to encode where particular
      functions and expressions can be evaluated to keep track of side effects while remaining functional
    - strengths:
        - confidence, simpler to reason about
        - referential transparency and elimination of mutable state
    - weaknesses:
        - efficiency (but generally less than predominantly reported, and worth it for gain in robustness and scalability)


# Functional Concurrency
# Persistent Data Structures
# Software Transactional Memory
# Actors
# Communicating Sequential Processes
# GPGPU Programming
# Lambda Architecture and MapReduce


# Actors
- CAF
- Akka


# Futures
- Folly


# Java


# Green threads vs. lightweight processes vs. coroutines vs fibers vs protothreads


# libuv
- cross-platform asynchronous I/O
- event loop backed by epoll, kqueue, IOCP, event ports.
- asynchronous TCP and UDP sockets
- asynchronous DNS resolution
- asynchronous file and file system operations
- file system events
- ANSI escape code controlled TTY
- IPC with socket sharing, using Unix domain sockets or named pipes (Windows)
- child processes
- thread pool
- signal handling
- high resolution clock
- threading and synchronization primitives

