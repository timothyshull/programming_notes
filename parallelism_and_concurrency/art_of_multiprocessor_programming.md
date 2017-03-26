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
