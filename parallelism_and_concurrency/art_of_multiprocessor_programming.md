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
- modern architectures provide instructions that overcome this limitation