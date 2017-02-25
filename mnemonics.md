# C strings
## Mnemonics
- 12 is aa lud x cgs bpp
- 2 to lu
- 4 a to filll
- 9 str to lll ul ull fd ld imax umax
- 5 str cpy ncpy cat ncat xfrm
- 11 str len cmp ncmp coll chr rchr spn cspn pbrk str tok
- 5 mem chr set cmp cpy move
- 1 strerror

## Full list
### Char classification - <cctype>
- isalnum
- isalpha
- islower
- isupper
- isdigit
- isxdigit
- iscntrl
- isgraph
- isspace
- isblank
- isprint
- ispunct

### Char manipulation - <cctype>
- tolower
- toupper

### Conversion to numbers - <cstdlib>
- atof
- atoi
- atol
- atoll

- strtol
- stroll
- strtoul
- strtoull
- strtof
- strtod
- strtold
- strtoimax
- strtoumax

### String manipulation - <cstring>
- strcpy
- strncpy
- strcat
- strncat
- strxfrm

### String examination - <cstring>
- strlen
- strcmp
- strncmp
- strcoll
- strchr
- strrchr
- strspn
- strcspn
- strpbrk
- strstr
- strtok

### Character array manipulation - <cstring>
- memchr
- memset
- memcmp
- memcpy
- memmove

### Interpret error codes as string - <cstring>
- strerror



# Algorithms
## Mnemonics
- Non-modifying - aan FF.CC. Me 5f ass
- Modifying - 4c 2m 2f t 2g 8r 2s i 4r rss 2u
- Partitioning - i 2p sp
- Sorting - 2i s 2p sn
- Binary search - lube
- Set ops - m 2i 4s
- Heap ops - iimpps
- Minmax ops - 6m clinp
- Numeric ops - iaia pre i 3t
- Uninitialized mem ops - cfmdv 3d
- C lib - qb

## Full list
### Non-modifying sequence operations
- all_of
- any_of
- non_of
- for_each
- for_each_n
- count
- count_if
- mismatch
- equal
- find
- find_if
- find_if_not
- find_end
- find_first_of
- adjacent_find
- search
- search_n

### Modifying sequence operations
- copy
- copy_if
- copy_n
- copy_backward
- move
- move_backward
- fill
- fill_n
- transform
- generate
- generate_n
- remove
- remove_if
- remove_copy
- remove_copy_if
- replace
- replace_if
- replace_copy
- replace_copy_if
- swap
- swap_ranges
- iter_swap
- reverse
- reverse_copy
- rotate
- rotate_copy
- random_shuffle *
- shuffle
- sample
- unique
- unique_copy

### Partitioning operations
- is_partitioned
- partition
- partition_copy
- stable_partition
- partition_point

### Sorting operations
- is_sorted
- is_sorted_until
- sort
- partial_sort
- partial_sort_copy
- stable_sort
- nth_element

### Binary search operations (on sorted ranges)
- lower_bound
- upper_bound
- binary_search
- equal_range

### Set operations (on sorted ranges)
- merge
- inplace_merge
- includes
- set_difference
- set_intersection
- set_symmetric_difference
- set_union

### Heap operations
- is_heap
- is_heap_until
- make_heap
- push_heap
- pop_heap
- sort_heap

### Minimum/maximum operations
- max
- max_element
- min
- min_element
- minmax
- minmax_element
- clamp
- lexicographical_compare
- is_permutation
- next_permutation
- prev_permutation

### Numeric operations
- iota
- accumulate
- inner_product
- adjacent_difference
- partial_sum
- reduce
- exclusive_scan
- inclusive_scan
- transform_reduce
- transform_exclusive_scan
- transform_inclusive_scan

### Operations on uninitialized memory
- uninitialized_copy
- uninitialized_copy_n
- uninitialized_fill
- uninitialized_fill_n
- uninitialized_move
- uninitialized_move_n
- uninitialized_default_construct
- uninitialized_default_construct_n
- uninitialized_value_construct
- uninitialized_value_construct_n
- destroy_at
- destroy
- destroy_n

### C library functions
- qsort
- bsearch



# Iterators

## Full list
### Iterator primitives
- iterator_traits
- input_iterator_tag
- output_iterator_tag
- forward_iterator_tag
- bidirectional_iterator_tag
- random_access_iterator_tag
- iterator

### Iterator adaptors
- reverse_iterator
- make_reverse_iterator
- move_iterator
- make_move_iterator
- back_insert_iterator
- back_inserter
- front_insert_iterator
- front_inserter
- insert_iterator
- inserter

### Stream iterators
- istream_iterator
- ostream_iterator
- istreambuf_iterator
- ostreambuf_iterator

### Iterator options
- advance
- distance
- next
- prev

### Range access
- begin
- cbegin
- end
- cend
- rbegin
- crbegin
- rend
- crend

### Container access
- size
- empty
- data



# Regular expressions library

## Full list
### Main classes
- basic_regex
- sub_match
- match_results

### Algorithms
- regex_match
- regex_search
- regex_replace

### Iterators
- regex_iterator
- regex_token_iterator

### Exceptions
- regex_error

### Traits
- regex_traits

### Constants
- syntax_option_type
- match_flag_type
- error_type



# Thread support library

## Full list
### Threads
- thread

### Functions managing the current thread
- yield
- get_id
- sleep_for
- sleep_until

### Cache size access
- hardware_destructive_interference_size
- hardware_constructive_interference_size

### Mutual exclusion
- mutex
- timed_mutex
- recursive_mutex
- recursive_timed_mutex
- shared_mutex
- shared_timed_mutex

### Generic mutex management
- lock_guard
- scoped_lock
- unique_lock
- shared_lock
- defer_lock_t
- try_to_lock_t
- adopt_lock_t
- defer_lock
- try_to_lock
- adopt_lock

### Generic locking algorithms
- try_lock
- lock

### Call once
- once_flag
- call_once

### Condition variables
- condition_variable
- condition_variable_any
- notify_all_at_thread_exit
- cv_status

### Futures
- promise
- packaged_task
- future
- shared_future
- async
- future_status

### Future errors
- future_error
- future_category
- future_errc





# Algorithms
## Sorting (ordered best - avg - worst)
- selection sort: 1/2n^2 - 1/2n^2 - 1/2n^2
- insertion sort: n - 1/4n^2 - 1/2n^2
- bubble sort: n - 1/2n^2 - 1/2n^2
- shellsort: 1/2nlog3(n) - unknown - cn^3/2
- mergesort: 1/2nlgn(n) - nlg(n) - nlg(n)
- quicksort: nlg(n) - 2nln(n) - 1/2n^2
- heapsort: n (nlg(n) for distinct keys) - 2nlg(n) - 2nlg(n)
- counting sort: n - n - n  (n + k -> k is the size of the count array)
- bucket sort: n - n - n^2 (n + k)
- radix sort: n - n - n (wn -> n word size)

### In place - not merge, count, bucket, radix (on a highway) (not mcbr)
- selection, insertion, bubble, shellsort, quicksort, heapsort

### Stable - ibm (IBM is a stable company)
- insertion, bubble, mergesort, counting


## Priority queues (ordered insert - delete min - min - dec-key - delete - merge)
- array: 1 - n - n - 1 - 1 - n
- binary heap: log(n) - log(n) - 1 - log(n) - log(n) - n
- d-way heap: logD(n) - dlogD(n) - 1 - logD(n) - dlogD(n) - n
- binomial heap: 1 - log(n) - 1 - log(n) - log(n) - log(n)
- Fibonacci heap: 1 - log(n) - 1 - 1 - log(n) - log(n)


## Symbol tables/search (ordered search (worst) - insert(worst) - delete(worst) - search(avg) - insert(avg) - delete(avg))
- sequential search: n - n - n - n - n - n
- binary search: log(n) - n - n - log(n) - n - n
- binary search tree: n - n - n - log(n) - log(n) - n^1/2
- red-black BST: log(n) - log(n) - log(n) - log(n) - log(n) - log(n)
- hash table (separate chaining): n - n - n - 1 - 1 - 1
- hash table (linear probing): n - n - n - 1 - 1 - 1


## Graph problems (ordered algorithm - time - space)
- path: DFS E + V V
- cycle: DFS E + V V
- directed cycle: DFS E + V V
- topological sort: DFS E + V V
- bipartiteness: / odd cycle DFS E + V V
- connected components: DFS E + V V
- strong components: Kosaraju–Sharir E + V V
- Eulerian cycle: DFS E + V E + V
- directed Eulerian cycle: DFS E + V V
- transitive closure: DFS V (E + V) V 2
- minimum spanning tree: Kruskal E log E E + V
- minimum spanning tree: Prim E log V V
- minimum spanning tree: Boruvka E log V V
- shortest paths (unit weights): BFS E + V V
- shortest paths (nonnegative weights): Dijkstra E log V V
- shortest paths (negative weights): Bellman–Ford - V*(V + E) - V
- all-pairs shortest paths: Floyd–Warshall - V^3 - V^2
- maxflow–mincut: Ford–Fulkerson - E*V*(E + V) - V
- bipartite matching - Hopcroft–Karp - V^1/2*(E + V) - V
- assignment problem: successive shortest paths - n^3 log(n) - n^2