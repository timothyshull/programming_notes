# Ch. 1 Introduction
- parallelism - using multiple processors to work on a single task
- shared-memory multiprocessors -
- multicores -
- aysnchronous -
- threads
- concurrent program
- concurrent algorithm
- program correctness
- safety property
- transactional memory

## Shared Objects and Synchronization
- race conditions are caused by indeterminate interleaved reads and writes
  from multiple threads
- modern processors provide read-modify-write instructions that allow
  threads to perform these operations atomically
- mutual exclusion - technique for making sure that only one thread at a time
  can execute a particular block of code at a time
- understanding mutual exclusion is necessary to understand concurrent
  computation (and key for deadlock, bounded fairness, and blocking vs.
  nonblocking synchronization)

## A Fable
- coordination protocol - agreed method for sharing access to a resource
- basic idea
    - actor 1
        1. set flag
        2. if actor 2's flag is unset, perform action
        3. on completion, unset flag
    - actor 2
        1. set flag
        2. if actor 1's flag is set
            1. unset flag
            2. wait until actor 1's flag is unset
            3. set flag
        3. on flag set (when actor 1's flag is unset) perform action
        4. on completion, unset flag
  - flag principal -
    - if actors 1 & 2
        1. set own flag
        2. check other's flag
    - then at least one will see the other's flag set and will not act
    - NOTE: does not prove actions never occur concurrently
    - to prove this, assume actions are occurring concurrently
        - for actor 1 to act, actor 2's flag was unset on check
        - for this, actor 2 must not have completed setting before
          actor 1 checked
        - this means when actor 2 checked last, it was after actor 1
          set the flag
        - this means actor 2 would not have acted and thus negates
          the possibility that they acted concurrently
- this description assumes nothing about the time period for setting
  or getting the state of the flag
- only concerned with start and end point of setting and getting, i.e.
  critical point of change

### Properties of Mutual Exclusion
- correctness of concurrent solutions depends on many properties, one of
  which is mutual exclusion
- deadlock-freedom - property that dictates that at least one action will
  always occur
- starvation-freedom (aka lockout-freedom) - property that dictates that
  one actors intention to act means that it will eventually act
- waiting - property that dictates that an actor will wait indefinitely
  according to the coordination protocol
- waiting is related to fault tolerance
- all mutual exclusion protocols require waiting
- other coordination protocols do not require waiting

### The Moral
- two kinds of communication occur naturally in concurrent systems
  - transient - both parties participate at same time
  - persistent - each party can participate at different times (using
    some form of persistent state)
- mutual exclusion requires persistent communication
- interrupts - common method for one thread to signal another (achieved
  by setting a bit at a predetermined location and checking that bit
  between threads)

## The Producer-Consumer Problem
- the producer's job is to generate data repetitively
- the consumer is consuming the data one piece at a time concurrently
- need to ensure the producer won't add data when it isn't possible and
  that the consumer won't try to remove data when it isn't possible
- basic idea
    - producer uses a flag and sets it when data is available
    - consumer
        1. waits for set flag
        2. acts on data
        3. on action completion, resets flag if possible
    - producer
        1. waits for flag unseet
        2. produces data
        3. sets flag
    - flag reflects data availability (set means available, unset = unavailable)
- properties
    - mutual exclusion - producer and consumer are never acting on data at same
      time
    - starvation-freedom - if producer continues to produce data and the consumer
      continues to consume data the consumer will always have data to consume
    - producer-consumer - the consumer will not attempt consume if there is no
      data and the producer will not produce data if the previously produced data
      has not yet been consumed
- the producer-consumer protocol cannot be used for mutual exclusion
    - mutual exclusion requires deadlock freedom which is not possible
      with the producer-consumer protocol because the consumer cannot
      consume if the producer has not produced and the producer cannot
      produce if the consumer has not consumed
 - proofs
    - mutual exclusion
        - the flag has two states and changes only occur after transitions
        - if the flag is set, only the consumer can consume
        - in order for the flag to be unset, the consumer must complete consumption
        - if the flag is unset, only the producer can produce
        - in order for the flag to be set, the producer must complete
        - mutual exclusion holds in each state
    - starvation-freedom
        - the consumer must be willing to consume infinitely often and the flag cannot
          be unset
        - the flag then must be set, and since the consumer is willing to consume,
          the consumer will consume until completion, thus unsetting the flag
    - producer-consumer
        - mutual exclusion implies the producer will never produce while the
          consumer consumes
        - the producer will not produce until until the consumer unsets the flag
          which can only be done if consumer is complete
        - the consumer will not consume until the flag is set which will not happen
          until the producer has completed production
- waiting
    - if producer produces but does not set the flag the consumer will
      never consumer
    - if the consumer consumes and does not unset the flag, the producer will
      never produce
- producer-consumer appears in almost all parallel and distributed systems
- it is the method used for communication buffers for transmission across a
  network or across a shared bus

## The Readers-Writers Problem
- actors want to communicate through a shared resource but cannot access
  the resource at the same time
- consider the shared resource to be composed of multiple elements
    - the writer can write and the reader may read an incomplete
      message before the writer writes again
- can use the mutual exclusion protocol to ensure only complete messages
  are read but does not ensure all messages will be read
- can use a flag to signal when a message can be read
- the readers-writers problem does not have solutions that do not require
  waiting

## The Harsh Realities of Parallelization
- increasing parallel processing should ideally increase computational power
  linearly
- most real-world computational problems do not display this linear increase
  because parallelizing computations incurs the cost of IPC and coordination
- Amdahl's Law - any potential possible speed increase of a complex task is
  limited by the required sequential execution of the job's component tasks
- TODO: more here

# Ch. 2 Mutual Exclusion
- covers classical algorithms that work by reading and writing shared memory

## Time
- concurrency requires reasoning about time and how intervals overlap
- threads share a common time (not necessarily a common clock)
- thread is a state machine
- events - state transitions
- events are instantaneous - they occur at a single instant in time
- convenient to require that events are never simultaneous and events
  that occur close in time can be assumed to be in any order
- a thread A has a set of events in running time a sub 0 ... a sub i
  and loops are thought of as a single event denoted by a sub i sup j
- a -> b means a precedence relation
- interval is duration between events and concurrent intervals have overlap
  between that duration

### Critical Sections
- a block of code that can be executed by only one thread at a time (aka
  mutual exclusion)
- standard way to achieve mutual exclusion is through a Lock object
```
class Lock {
public:
    void lock();
    void unlock();
};
```
- acquires/locks, releases/unlocks
- thread is well formed if
    1. each critical section is associated with a unique Lock object
    2. thread acquires the critical section at the start of each
       critical section
    3. the thread releases the lock when it leaves the critical section
- in Java use a try...finally block to ensure the lock is always unlocked
```
mutex.lock();
try {
    // do work with shared data
} finally {
    mutex.unlock();
}
```
- properties for a lock algorithm
    - mutual exclusion - running of critical sections within different
      threads does not overlap (i.e. interval/duration between lock event
      and unlock event)
    - freedom from deadlock - some thread will always succeed in acquiring
      the lock when one or more threads are attempting to acquire the lock
      (if a thread attempts to acquire the lock and never does it means
      other threads are completing infinitely many critical sections)
    - freedom from starvation (aka lockout freedom) - every thread that
      attempts to acquire the lock eventually succeeds (i.e. every call
      to lock eventually returns

## Thread Solutions
### The LockOne Class
- 2-thread lock
- code here
- generally use the volatile keyword to ensure the compiler does not reorder
  or optimize out instructions
- use write sub A(x = v) to denote a write event and read sub A(x == v) to
  denote a read event
- Lemma 2.3.1 -> LockOne class satisfies mutual exclusion
- provides proof here
- deadlocks if thread executions are interleaved
- if one thread runs before the other then no deadlock occurs

### The LockTwo Class
- 2-thread lock
- code here
- Lemma 2.3.2 -> LockTwo satisfies mutual exclusion
- provides proof here
- deadlocks if one thread runs completely ahead of the other
- if the threads run concurrently, lcok() succeeds
- LockOne and LockTwo complement each other, one succeeds under condition
  that another fails

## The Peterson Lock
- combining LockOne and LockTwo creates a starvation-free Lock algorithm
- very elegant and succinct two-thread mutual exclusion algorithm
- code here
- Lemma 2.3.3 -> Peterson lock satisfies mutual exclusion
- provides proof here
- Lemma 2.3.4 -> Peterson lock is starvation-free
- provides proof here
- Corollary 2.3.1 -> Peterson lock is deadlock-free

## The Filter Lock
- two solutions that work for n > 2 threads
- Filter lock uses n - 1 waiting levels that a thread must traverse
  before acquiring a lock
- levels satisfy the following properties
    - at least one thread attempting to satisfy level l succeeds
    - if more than one thread is trying to enter level l then at least
      one is blocked (i.e. will wait at level)
- Peterson lock uses a two-element boolean flag array to indicate whether
  a thread is trying to enter the critical section
- Filter lock generalizes this idea with an n-element integer level array
- `level[A]` indicates the highest level that thread A is currently trying
  to enter
- levels = levels of exclusion
- each level has a distinct `victim[l]` field to filter out a thread excluding
  it from the next level
- Lemma 2.4.1 -> for j between 0 and n - 1, there are at most n - j threads
  at level j
- proof here
- Corollary 2.4.1 -> the Filter lock satisfies mutual exclusion
- Lemma 2.4.2 -> the Filter lock is starvation-free
- proof here
- Corollary 2.4.2 -> the Filter lock is deadlock-free

## Fairness
- starvation-freedom guarantees every thread that calls lock() eventually
  enters the critical section but does not guarantee duration in critical
  section
- informally, if A calls lock() before B it should enter the critical
  section before B
- cannot determine which thread called lock() first with current implementation
- to facilitate this, split lock into two sections
    1. doorway -> execution interval D sub A consists of a bounded number
       of steps
    2. waiting -> execution interval W sub A can consist of an unbounded
       number of steps
- bounded wait-free progress property -> requirement that doorway will always
  finish in a bounded number of steps

#### Fairness
- Definition 2.5.2 -> a lock is first-come-first-served if, when thread A
  finishes its doorway before thread B finishes its doorway, then A cannot
  be overtaken by B

## Lamport's Bakery Algorithm
- maintains the first-come-first-served property by using a distributed version
  of the number-dispensing machines (i.e. machines that distribute an order
  to elements in a queue)
- each thread takes receives a number when it enters the doorway and then
  waits until no thread with an earlier number is trying to enter the doorway
- code here
- `flag[A]` is a boolean flag indicating whether A wants to enter the critical
  section, and `label[A]` is an integer that indicates the thread’s relative
  order when entering the doorway of the critical section, for each thread A
- generates a new label by
    1. reads all other threads' labels in any order
    2. reads all other threads' labels in some arbitrary order and generates
       a label that is one greater than the maximal label
- uses lexicographic ordering on pairs of labels and thread ids
- labels are strictly increasing
- labels are read asynchronously so the set of labels read by a thread in a
  section may not have existed in memory at the same time but the algorithm
  still works
- Lemma 2.6.1 -> Bakery lock algorithm is deadlock-free
- proof here
- Lemma 2.6.2 -> Bakery lock algorithm is first-come-first-served
- any algorithm that is both deadlock-free and first-come-first-served
  is also starvation free
- Lemma 2.6.3 -> Bakery algorithm satisfies mutual exclusion
- proof here

## Bounded Timestamps
- labels of Bakery grow without bound which will eventually result in overflow
- overflow will negate the first-come-first-served property
- labels in the Bakery lock act as timestamps
- threads need two capabilities
    - read the other threads' timestamps (i.e. scan)
    - assign itself a later timestamp (i.e. label)
- code for Timestamp system here
- must be wait-free (see references for how to construct wait-free concurrent
  timestamp system)
- simpler problem:
    - construct a sequential timestamping system
    - threads perform scan-and-label sequentially (as if with mutual exclusion)
- underlying principles of sequential vs. concurrent timestamping are similar
  but differ in detail
- think of timestamps as nodes in a directed graph (called a precedence graph)
- edge from a -> b means b occurs after a
- irreflexive -> no edge from any node a to itself
- antisymmetric -> if edge a -> b, no edge b -> a
- no requirement of transitivity -> edges a -> b and b -> c do not imply
  an edge a -> c exists
- assigning a timestamp to a thread = placing the thread's token on the
  timestamp's node
- see this section for a more detailed description of the n-thread labeling
  system
- note -> two threads can never form a cycle

## Lower Bounds on the Number of Locations
- Bakery algorithm is not practical because need to read and write
  n locations, where n is the maximum number of concurrent threads
  (which may be very large)
- no lock algorithm avoids this overhead
- any deadlock-free Lock algorithm requires allocating and then reading
  or writing at least n distinct locations in the worst case
- limitation of memory accessed solely by reads/writes (aka loads/stores):
    - any information written by a thread to a given location can
      be overwritten without any other thread observing it
- requires definitions of state for memory
    - object's state -> state of object's fields
    - thread's local state -> state of program counters and local variables
    - global/system state -> state of all global objects + local state of
      threads
- Definition 2.8.1
    - inconsistent state -> a lock object's state, s, is inconsistent in any
      global state where some thread is in the critical section,
      but the lock state is compatible with a global state in which no
      thread is in the critical section or is trying to enter the critical
      section
- Lemma 2.8.1 -> no deadlock-free lock algorithm can enter an inconsistent
  state
- proof here
- Definition 2.8.2
    - covering state -> a covering state for a lock object is one in which
      there is at least one thread about to write to each shared location,
      but the Lock object's locations present the appearance that the
      critical section is empty (i.e. the locations' states appear as if
      there is no thread either in the critical section or trying to enter
      the critical section)
- Theorem 2.8.1 -> any lock algorithm that, by reading and writing memory,
  solves deadlock-free mutual exclusion for three threads must use at least
  three distinct memory locations
- proof here
- it follows that n-thread deadlock-free mutual exclusion requires
  n distinct memory locations
- the Peterson and Bakery locks are optimal within a constant factor
- limitation of read and write operations:
    - information written by a thread may be overwritten without any
      other thread reading it
- modern architectures provides instructions that overcome this limitation

# Ch. 3 Concurrent Objects
- safety and liveness properties aka correctness and progress

## Concurrency and Correctness
- shows lock-based concurrent FIFO queue that stores a lock and locks
  in both the enqueue and dequeue methods
- implementation allows multiple enqueuers/dequeuers
- shows lock-free/wait-free concurrent queue
- implementation presents a single enqueuer/single dequeuer queue
  which means the queue can be accessed from two separate threads,
  one thread which performs enqueueing and one thread that performs
  dequeueing
- a result of Amdahl's law is that concurrent objects that use exclusive
  locks within their methods are worse than ones that use finer-grained
  locking and those are worse than ones that do not use locks at all
- easier to reason about concurrent objects if their execution can
  be mapped to a sequential model
- this principal is key to correctness properties

## Sequential Objects
- precondition -> some required state that an object (including global state
  when also accounting for arguments) must be in before executing a method
- postcondition -> the state of an object (and global state) after a method's
  execution, including return value
- side effect -> change to an object's state (or the global state)
- note: references to global state above includes expanding containing scopes
  but this is not mentioned in the book
- sequential specification -> documentation of the full set of preconditions
  and postconditions possible for a method (can ignore intermediate states)
- cannot assume so much when considering method interactions etc. when there
  is concurrent access to an object (mainly due to the potential for method
  overlap, which means intermediate states must also be considered)

## Quiescent Consistency
- method calls take time an start with an invocation event and a response
  event (which should appear to take effect instantaneously)
- sequential method calls will never overlap but concurrent method calls
  may overlap
- pending -> a method call whose invocation event has occurred but whose
  response event has yet to occur
- register -> object version of a read-write memory location (i.e. a data
  member or property) NOTE: this name persists due to historical reasons
  despite confusing relationship
- Principle 3.3.1 ->
    - method calls should appear to happen in a one-at-a-time sequential order
- quiescent -> an object is quiescent if it has no pending method calls
- Principle 3.3.2 ->
    - method calls separated by a period of quiescence should appear to take
      effect in their real-time order
- quiescent consistency -> any time an object becomes quiescent then the
  execution so far is equivalent to some sequential execution of the completed
  calls
- a quiescently consistent counter is an index distribution mechanism
  that can be used as a loop counter but the clients of the loop counter
  must not be concerned about the order in which the indexes are used

### Remarks
- quiescent consistency never requires one method call to block waiting for
  another to complete
- total method -> defined for every object state
- partial method -> not defined for every object state
- in any concurrent execution, for any pending invocation of a total method,
  there exists a quiescently consistent response
- quiescent consistency is a nonblocking correctness condition
- compositional -> a correctness property, P, is compositional if, whenever
  each object in the system satisfies P, the system as a whole satisfies P
- compositionality is important for large systems and closely related to
  modularity (using hidden implementations and exposed interfaces)
- the consistency guarantees are exposed through the interface as well
- quiescent consistency is compositional

## Sequential Consistency
- program order -> the order in which a single thread issues method calls
- method calls by different thread are unrelated by program order
- Principle 3.4.1 ->
    - method calls should appear to take effect in program order
- sequential consistency -> a correctness property that combines principles
  3.3.1 and 3.4.1, e.g.
    - sequential consistency requires that method calls act as if they occurred
      in a sequential order consistent with program order
- in any concurrent execution, there is a way to order methods call sequentially
  so that they
    1. are consistent with program order
    2. meet the object's sequential specification

### Remarks
- sequential consistency and quiescent consistency are incomparable
    - there exist sequentially consistent executions that are not
      quiescently consistent, and vice versa
- in most modern multiprocessor architectures, memory reads are not sequentially
  consistent
    - the can be typically reordered (without notification)
- memory barriers or fences -> special instructions provided by multiprocessor
  architectures that instruct the processor to propagate updates to and from
  memory to ensure that reads and writes interact correctly
- sequential consistency
    - nonblocking
    - not compositional

## Linearizability
- the result of composing sequentially consistent components is not itself
  necessarily sequentially consistent
- Principle 3.5.1 ->
    - each method call should appear to take effect instantaneously at some
      moment between its invocation and response
- real time behavior of method calls must be preserved
- linearizability -> Principle 3.5.1
- every linearizable execution is sequentially consistent but not vice versa

### Linearization Points
- to show that a concurrent object is linearizable is to identify, for each
  method, a linearization point
- for lock based implementations, each method's critical section can serve
  as its linearization
- for implementations that do not use locking, the linearization point is
  typically a single step where the method call becomes visible to other
  method calls

### Remarks
- sequential consistency is useful for describing standalone systems
- linearizability is useful for describing components of large systems where
  components must be implemented and verified independently
- linearizabilty is compositional and nonblocking (and as a result does not
  limit concurrency)

## Formal Definitions
- informally, linearizability means each method call appears to take effect
  instantaneously at some moment between that method's invocation and return
  events
- history -> a finite sequence of method invocation and response events
- subhistory -> a subsequence of events within a history
- invocation -> <x.m(a*)A>
    - x is an object
    - m a method name
    - a* a sequence of arguments
    - A is a thread
- response -> <x:t(r*)A>
    - t is OK or an exception
    - r* is a sequence of result values
- step of A -> an event labeled with thread A
- response matches an invocation if they have the same object and thread
- method call (formally) -> in a history H, a method call is a pair consisting
  of an invocation and the next matching response in H
- pending -> an invocation is pending in H if no matching response follows
  the invocation
- extension -> an extension of a history H is a history constructed by
  appending responses to zero or more pending invocations of H
- complete(H) -> the subsequence of H consisting of all matching invocations
  and responses, i.e. ignore all pending invocations
- a history H is sequential if the first event of H is an invocation, and
  each invocation, except possibly the last, is immediately followed by a
  matching response (i.e. method calls do not overlap)
- thread subhistory -> a history of H is the subsequence of all events in H
  whose thread names are equivalent
- object subhistory -> a history of H is the subsequence of all events in H
  whose object is equivalent
- equivalent (for histories) -> two histories (H and H') are equivalent if
  for every thread, A, in H, H|A = H'|A
- well formed (history) -> if each thread subhistory is sequential
- sequential specification (for an object) -> a set of sequential histories
  for the object
- legal (subhistory) -> a sequential history, H, is legal if each object subhistory
  is legal for that history
- partial order
    - irreflexive and transitive
    - never true that x -> x
    - when x -> y and y -> z then z -> z
- total order (on X)
    - a partial order such that for all distinct x and y in X either x < y or y < x
- any partial order can be extended to a total order
- Fact 6.3.1 ->
    - if -> is a partial order on X, then there exists a total order "<"
      on X such that if x -> y, then x < y
- a method call m0 precedes method call m1 in history H if m0 finished
  before m1 (i.e. m0's response event occurs before m1's invocation event)

### Linearizability
- basic idea -> event concurrent history is equivalent, according to
  the following sense, to some sequential history
    - if one method call precedes another, then the earlier call must have taken effect
      before the later call
    - by contrast, if two method calls overlap, then their order is ambiguous,
      and they can be ordered in any convenient way
- Definition 6.3.1 ->
    - a history is linearizable if it has an extension H' and there is a
      legal sequential history S such that
        - complete(H') is equivalent to S
        - if method call m0 precedes method call m1 in H, then the same is
          true in S
- S is a one (of potentially many) linearizations in S

### Compositional Linearizability
- Theorem 3.6.1 ->
    - H is linearizable if and only if for each object x, H|x is linearizable
- proof here
- compositionality is important for composing modular concurrent systems
- basing a concurrent system on a noncompositional correctness property means
  it must rely on a centralized scheduler for all objects or it must satisfy
  additional constraints placed on objects to ensure that they follow compatible
  scheduling protocols

### The Nonblocking Property
- a pending invocation of a total method is never required to wait for another
  pending invocation to complete
- Theorem 3.6.2 ->
    - let inv(m) be an invocation of a total method
    - if <x inv P> is a pending invocation in a linearizable history H, then
      there exists a response <x res P> such that H * <x res P> is linearizable
- proof here
- linearizability by itself never forces a thread with a pending invocation
  of a total method to block
- the nonblocking property does not rule out blocking in situations where it
  is explicitly intended

## Progress Conditions
- blocking -> a delay (possibly unexpected) can prevent others from making
  progress
- delays can be caused by
    - cache misses
    - page faults
    - preemption
- wait-free -> a call finishes its execution in a finite number of steps
- bounded wait-free -> a bound exists on the number of steps a method call
  can take
- population-oblivious -> a wait-free method whose performance does not depend
  on the number of active threads
- nonblocking (progress condition) -> being wait-free
    - an arbitrary and unexpected delay by one thread (possibly one holding
      a lock) does not necessarily prevent the others from making progress
- wait-free property is a good one because it guarantees that every thread
  that takes steps makes progress
    - can be inefficient
- lock-free (method) -> a method that guarantees that infinitely often some
  method call finishes in a finite number of steps
- wait-free is lock-free but lock-free does not mean wait-free
- lock-free algorithms can lead to starvation
    - a fast lock-free algorithm may be more attractive than a slower wait-free
      algorithm

### Dependent Progress Conditions
- wait-free and lock-free nonblocking progress conditions guarantee that the
  computation as a whole makes progress, independently of how the system
  schedules threads
- dependent progress conditions -> progress occurs only if the underlying
  platform (i.e. OS) guarantees
    - every thread eventually leaves every critical section
    - (useful when the OS guarantees that every thread eventually leaves
      every critical section in a timely manner)
- the deadlock-free and starvation-free properties are dependent progress
  conditions
- lock-based synchronization can guarantee, at best, dependent progress
  conditions
- isolated execution -> a method call executes in isolation if no other threads
  take steps
- Definition 3.7.1 ->
    - obstruction-free -> a method is obstruction-free if, from any point after
      which it executes in isolation, it finishes in a finite number of steps
- the obstruction-free property
    - is a dependent nonblocking progress condition
    - rules out the use of locks
    - only requires pausing threads that conflict, i.e. they call the same
      shared object's methods
- back-off mechanism -> a thread that detects a conflict pauses to give an
  earlier thread time to finish
- the simplest way to exploit an obstruction-free algorithm is to introduce
  a back-off mechanism

## The Java Memory Model
- does not guarantee linearizability or sequential consistency when reading
  or writing fields of shared objects
    - caused by compiler optimizations that reorder reads/writes
        - register allocation
        - common subexpression elimination
        - redundant read elimination
- satisfies the Fundamental Property of relaxed memory models
    - for a sequentially consistent execution, if certain rules are followed,
      then every execution of that program in the relaxed memory model will
      still be sequentially consistent
- all rules are not covered
- double-checked locking -> a once-common idiom in Java that checks for a null
  instance, enters a synchronized block, and checks for a null instance again
  before performing the action
- see 3.10 for double-checked locking with a Singleton
- it does not work as expected due to the lack of sequential consistency
  in Java's memory model because of instruction reordering
- objects reside in a shared memory and each thread has a private working
  memory that contains cached copies of fields it has read or written
- each JVM can keep cached fields in threads consistent but it is not
  required to
- synchronization events -> statements that in other contexts imply atomicity
  or mutual exclusion but within the JVM implies reconciling a thread's
  working memory with the shared memory
    - update shared memory with cached memory changes
    - invalidate cached values and reread from shared memory
- synchronization events are linearizable
    - they are totally ordered, and all threads agree on that ordering

### Locks and Synchronized Blocks
- a thread achieves mutual exclusion
    - by entering a synchronized block or method (which acquires an implicit lock)
    - or by acquiring an explicit lock (e.g. ReentrantLock, FileLock, StampedLock,
      ReadWriteLock)
- if all accesses to a particular field are protected by the same lock
  then reads-writes to that field are linearizable

### Volatile Fields
- linearizable
- reading is like acquiring a lock
    - working memory is invalidated and volatile field's current value
      is reread from memory
- writing is like releasing a lock
    - the volatile field is immediately written back to memory
- multiple reads/writes are not atomic
- may still need locks

### Final Fields
- final is const and cannot be modified once it has been initialized
- initialized in ctor
- reads/writes to fields are linearizable if either the field is volatile,
  or the field is protected by a unique lock which is acquired by all
  readers and writers

## Remarks
- different methods for a concurrent object can have different progress
  conditions
- frequently called time-critical, read method should be wait-free
- infrequent writes can use mutual exclusion
