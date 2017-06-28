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

## Generators

## concurrent.futures
- use for true parallelism

- ThreadPoolExecutor
- ProcessPoolExecutor

## socket

## select

## epoll

## threading
- use for blocking I/O and not parallelism
- use Lock to prevent data races in threads
- use Queue to coordinate work between threads

## asyncio

## subprocess


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


# SCMSW
- Threads and Locks
- Functional Parallelism
- Functional Concurrency
- Persistent Data Structures
- Software Transactional Memory
- Actors
- Communicating Sequential Processes
- GPGPU Programming
- Lambda Architecture and MapReduce
