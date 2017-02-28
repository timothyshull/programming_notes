# Memory Management

## DSA C++
- general ideas behind basic malloc, realloc, free/new & delete
- different levels of memory management (hardware MMU, run time, garbage collection)
- external and internal fragmentation management

### Sequential-fit
- for efficiency, use doubly linked list inside a block of memory, also maintaining
  a status field and a size field
- approaches:
    - first-fit: scan through the list and allocate the first block of memory large
      enough to meet the request
    - best-fit: scans through to find the block that is closest in fit to the requested
      size
    - worst-fit: use larges block in list so that the remaining portion is large enough
      to be used in future requests
    - next-fit: use the next available block that is sufficiently large
- analysis:
    - first-fit: most efficient
    - next-fit: comparable in speed but causes more external fragmentation
    - best-fit: causes the most fragmentation
    - worst-fit: avoids fragmentation in some ways
- fragmentation may be more dependent on the implementation than the method

### Nonsequential-fit
- sequential-fit methods may become inefficient for large memory
- approach:
    - divide memory into an arbitrary number of lists, each holding blocks of the same
      size
    - larger blocks are split and the remainder is added to other lists or new lists
      are created if necessary
    - list sizes can be ordered into trees (Cartesian trees)
    - variants of first-fit and best-fit can be applied (i.e leftmost-fit and better-fit)
- approach:
    - programs have a limited number of sizes requested
    - use the above list approach with the most popular block sizes
    - known as the adaptive exact-fit approach
- need to handle memory fragmentation:
    - occasional compaction
    - can also liquidate size list and rebuild

### Buddy Systems


# Caching
## FIFO

## LRU (Least recently used)

## LFU


- NOTE: Knuth and Compiler Techniques have more discussions in this area

- memory management
    - sequential-fit methods
    - nonsequential-fit methods
    - garbage collection
        - mark-and-sweep
        - copying methods
        - incremental
        - generational
    - caching