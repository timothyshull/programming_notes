# Elementary Sorts
- always consider that complex sorts often only become useful (or faster) when 
  considering large datasets
- generally N^2 for basic datasets (mostly worst case)
- think of sorting as the sorting of files of items containing keys
- if the file fits in memory it is called internal sorting, otherwise it is called
  external sorting
- consider both arrays and linked lists

- NOTE: pass upper and lower array bounds in C/C++ to make the interfaces more flexible

- adaptive sort - a sort that performs different sequences of operations depending on 
  the output of comparisons (or the initial state of the data)
  
- non-adaptive sort - a sort that performs the same sequence of operations on all
  input data
  
- performance:

```
N^2                 NlogN           N^3/2
bubble sort                         shellsort
selection sort
insertion sort

```

- two best approaches to improving performance:
    - find a better algorithm
    - tighten inner loops
    
- consider memory cost also
    - in place sorts (0 extra memory)
    - linked list sorts that need N extra memory for pointers
    - sorts that require a temporary second copy of the data
    
- stable sorts preserve the relative order of items with duplicate keys
    - stable: insertion, merge, bubble
    - not stable: heap, quick, shell

- two types of sorting, by keys or items
- indirect sort -> sort an array of pointers, references, or indices rather than
  the items themselves
  

## Selection sort
- general properties:
    - not stable
    - O(1) extra space
    - Θ(n ^ 2) comparisons
    - Θ(n) swaps
    - not adaptive

- find smallest element, exchange with element in first position
  find smallest element in remaining and exchange with element in 2nd position
  etc.
  
```
template<typename Comparable>
void sort(Comparable a[], size_t size) {  // Sort a[] into increasing order.
    for (int i = 0; i < size; i++) {  // Exchange a[i] with smallest entry in a[i+1...N).
        int min = i;                 // index of minimal entr.
        for (int j = i + 1; j < size; j++) {
            if (a[j] < a[min])) {
                min = j;
            }
        }
        exch(a, i, min);
    } 
}
```

## Insertion sort
- general properties:
    - stable
    - O(1) extra space
    - O(n ^ 2) comparisons and swaps
    - adaptive: O(n) time when nearly sorted
    - very low overhead

- the C++ version provides performance improvements over standard versions

```
template<typename Comparable>
void sort(Comparable a[], size_t size) {  // Sort a[] into increasing order.
    for (int i = 1; i < N; i++) {  // Insert a[i] among a[i-1], a[i-2], a[i-3]... ..
        for (int j = i; j > 0 && a[j] < a[j-1]; j--) {
            exch(a, j, j - 1);
        }
    } 
}
```

## Bubble sort
- general properties:
    - stable
    - O(1) extra space
    - O(n ^ 2) comparisons and swaps
    - adaptive: O(n) when nearly sorted

- very similar to insertion sort, review Java code to determine exact differences
- the difference between the two is that in insertion sort the inner loop moves through
  the left sorted part of the array and in bubble sort it moves through the right, unsorted part of the array
- the inner loop can also be optimized as above
- can remove the non-adaptive compexch and and test for no exchanges in an inner loop and break out of outer loop
  
## Performance
- selection, insertion, and bubble are all quadratic time (N^2)
- generally proportional to the number of compares, the number of exchanges, or both
- one exchange for each i from 1 to N - 1 and N - i comparisons, totals to N - 1 exchanges and ((N - 1) + (N - 2) + ... + 2 + 1) = N * (N - 1) / 2 comparisons
- insertion sort uses N ^ 2 / 4 comparisons and N ^ 2 / 4 exchanges on average and twice that at worst
- insertion sort uses N ^ 2 / 2 comparisons and N ^ 2 / 2 exchanges on average and in the worst case

- insertion sort and selection sort a 2 X bubble sort for smaller input
- all three are bad for large randomly ordered inputs
- when comparisons are expensive, insertion sort is faster
- when exchanges are expensive, selection sort is best

- an inversion is a pair of keys out of order in an input set
- the inversion count for an input set indicates how far (or close) the array is from/to being sorted
- if an input set is already sorted then the inversion count is 0
- if an input set is sorted in reverse order then the inversion count is at a max 
- two elements a[i] and a[j] form an inversion if a[i] > a[j] and i < j

- insertion uses linear number of comparisons and exchanges when the input is constant having more than a constant number of inversions
- selection sort runs in linear time for files with large items and small keys

# Shellsort
- general info:
    - not stable
    - O(1) extra space
    - O(n ^ 3/2) time as shown * 
    - Adaptive: O(n·lg(n)) time when nearly sorted

- insertions sort moves items one place at a time, shellsort is like insertion sort but allows spaced exchanges
- shellsort takes every nth element starting from any position, sorts a segment and then interleaves segments

```
template<typename Comparable>
void sort(Comparable a[], size_t size) {
    int h = 1;
    while (h < size / 3) {
        h = 3 * h + 1;
    }
    for (int i = h; i < size; i++) {
        while (h >= 1) {
            for (int j = i; j >= h && less(a[j], a[j - h]); j -= h) {
                exch(a, j, j - h);
            }
            h = h / 3;
        }
    }
}
```

- general process -> for a modularity of h = 3 * h + 1, insertion sort each modular indexed element starting from 0
  reduce h to h / 3, repeat
- different increment sequences can improve worst case performance for large files
- current was suggested by Knuth in 1969
- there are bad sequences including the original proposed by Shell
- the precise formula for running times is not known
- properties
    - each pass brings the file closer to sorted order
    - the result of h-sorting an input sequence that is k-ordered (sorted) results in a sequence that is h and k-ordered
    - does less than N(h - 1)(k - 1) / g comparisons to g-sort a sequence that is h and k-sorted
    - does less than O(N ^ 3/2) for h = 1,4,13,40,121,364,1093
    - does less than O(N ^ 4/3) for h = 1,8,23,281,1073,4193
    - does less than O(N(log N) ^ 2) for 1,2,3,4,6,9,12,18,27,16,24,36
    
- performant increment sequences:

```
- 1 2 4 8 32 64 128 256 512 1024 2048 // not great
- 1 4 13 40 121 364 1093 3280 9841
- 1 2 4 10 23 51 113 249 548 1207 2655 5843 *
- 1 8 23 77 281 1073 4193 16577 *
- 1 7 8 49 56 64 343 392 448 512 2401 2744
- 1 5 19 41 109 209 505 929 2161 3905 * BEST
```