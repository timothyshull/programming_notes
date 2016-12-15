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

- basics:
```
template<class Item>
void exch(Item a[], size_t i, size_t j) {
    Item t = a[i];
    a[i] = a[j];
    a[j] = t;
}

template<class Item>
void compexch(Item a[], size_t i, size_t j) { if (a[j] < a[i]) { exch(a, i, j); }}

template<class Item>
void sort(Item a[], int l, int r) {
    for (int i = l + 1; i <= r; i++) {
        for (int j = i; j > l; j--) {
            compexch(a, a[j - 1], a[j]);
        }
    }
}

template<class Item>
void exch(Item a[], size_t i, size_t j) {
    Item t = a[i];
    a[i] = a[j];
    a[j] = t;
}

template<class Item>
void compexch(Item a[], size_t i, size_t j) { if (a[j] < a[i]) { exch(a, i, j); }}

template<class Item>
void sort(Item a[], int l, int r) {
    for (int i = l + 1; i <= r; i++) {
        for (int j = i; j > l; j--) {
            compexch(a, j - 1, j);
        }
    }
}
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
        for (int j = i; j > 0 && a[j] < a[j - 1]; j--) {
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

```
template<typename Comparable>
void sort(Comparable a, size_t size) {
    for (int i = 0; i < size; i++) {
        for (int j = r; j > i; j--) {
            compexch(a, j - 1, j);
        }
    }
}
```

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

# Sorting of Other Types of Data
- need to move to a more general type than arrays of numbers or characters
- generic array driver components:
    - generic ADT to encapsulate many data types
    - an exchange and compare/exchange functions (swap and compare/swap)
    - initialization of randomized data and the print functions
    - (optionally a timer etc)
- array ADT:
    - random (initialization)
    - scan (initialization from standard in)
    - print
    - sort
- item ADT:
    - key
    - value
    - operator < (or compareTo)
    - scan (init from std in)
    - random (init)
    - print

# Index and Pointer Sorting
- for string sorting the item ADT just contains a pointer to the str
```
struct Item {
    char *str;
}
```
- use a struct to facilitate operator overloading
- many options for custom string interfaces (dynamic memory, etc)
- always faced with memory management questions when working with
  pointers -> who is the owner? the client, the ADT, or the system?

## Sorting tactics
- When sorting arrays of large items it can be more performant to maintain
  a separate array of indices into the original array
- using string ADT above:
```
bool operator<(const Item &a, const Item &b) {
    return strcomp(a.str, b.str) < 0;
}
```
- using a separate array (this has some confusing issues about where data is)
```
bool operator<(const Index &i, const Index &j) {
    return data[i] < data[j];
}
```

## qsort
- pointer sort which takes a comparator (comparison function) as an argument
```
void qsort(array, array size, size of object, comparator)
int predicate(const void *a, const void *b) -> returns -1, 0, 1

std::qsort(a, size, sizeof *a, [](const void* a, const void* b) {
    int arg1 = *static_cast<const int*>(a);
    int arg2 = *static_cast<const int*>(b);

    if(arg1 < arg2) return -1;
    if(arg1 > arg2) return 1;
    return 0;

//  return (arg1 > arg2) - (arg1 < arg2); // possible shortcut
//  return arg1 - arg2; // erroneous shortcut (fails if INT_MIN is present)
});
```

## Separate array
- the container class can have two arrays
```
data[]
sorted_data[]
```
- access:
```
data[sorted_data[k]]
```
- in place sort (in situ permutation) NOTE: this might have problems
```
template<typename Item>
void in_situ(Item data[], Index a[], int N) {
    for (int i = 0; i < N; i++) {
        Item v = data[i];
        int j;
        int k;
        for (k = i; a[k] != i; k = a[j], a[j] = j) {
            j = k;
            data[k] = data[a[k]];
        }
        data[k] = v;
        a[k] = k;
    }
}
```

- also see heapsort
- Djikstra smoothsort (not stable)
    - https://en.wikibooks.org/wiki/Algorithm_Implementation/Sorting/Smoothsort

# Sorting of Linked Lists
- uses same interface
    - random initialization
    - initialization from standard in
    - printing
    - sorting
- memory is managed by the list itself
- one implication that is not always apparent is that only links
  in nodes should be changed, leaving keys/data untouched
- insertion, selection, and bubble can be adapted but present certain challenges

- NOTE: these assume the list head is not a nullptr
- linked list selection sort:
```
node *list_selection_sort(node *list_head) {
    node dummy{0, list_head};
    node *dummy_head = &dummy;
    node *out{nullptr};

    while (dummy_head->next != nullptr) {
        node *before_max = dummy_head;
        node *tmp = before_max;
        while (tmp->next != nullptr) {
            if (tmp->next->item > before_max->next->item) {
                before_max = tmp;
            }
            tmp = tmp->next;
        }
        tmp = before_max->next; // actual node containing max item
        before_max->next = tmp->next; // remove max node from input list
        tmp->next = out; // current max points to previous max
        out = tmp; // output list head is current max
    }

    return out;
}
```

- linked list insertion sort:
```
node *list_insertion_sort(node *list_head) {
    node dummy{0, nullptr};
    node *dummy_head = &dummy;
    node *i, *j, *tmp;

    for (tmp = list_head->next; tmp != nullptr; tmp = i) {
        i = tmp->next;
        for (j = dummy_head; j->next != nullptr; j = j->next) {
            if (j->next->item > tmp->item) {
                break;
            }
        }
        tmp->next = j->next;
        j->next = tmp;
    }

    return dummy.next;
}
```

- linked list bubble sort:
```
node *list_bubble_sort(node *list_head) {
    node dummy{0, list_head};
    node *dummy_head = &dummy;
    node *i, *j, *t1, *t2;

    for (i = list_head->next; i != nullptr; i = i->next) {
        for (j = dummy_head; j->next != nullptr; j = j->next) {
            t1 = j->next;
            t2 = t1->next;
            if (t2 != nullptr && t2->item < t1->item) {
                t1->next = t2->next;
                t2->next = t1;
                j->next = t2;
            }
        }
    }

    return dummy.next;
}
```

- may sometimes use algorithms with sorted linked lists (for sets, if insertions
  are relatively rare, or if the list is small)

# Key-Indexed Counting
- some keys have special properties such that they enable special sort techniques
- for integer keys
```
for (i = 0; i < n; i++) {
    b[key(a[i])] = a[i];
}
```
- can also use an array of counts for input arrays with duplicates
```
for (i = 0; i < n; i++) {
    b[cnt[a[i]]++] = a[i];
}
```
- key indexed counting:
    1. initialize counts to 0
    2. get counts by incrementing `a[i] + l`
    3. adds numbers to produce the count of the keys less than or equal to the current count
    4. use these numbers as indices into a temporary array representing the actual location within
       the array for l/r
    5. move values from b back to a

```
void distcount(int a[], int l, int r) {
    int i, j, cnt[M];
    static int b[maxN];
    for (j = 0; j < M; j++) { cnt[j] = 0; }
    for (i = l; i <= r; i++) { cnt[a[i] + 1]++; }
    for (j = 1; j < M; j++) { cnt[j] += cnt[j - 1]; }
    for (i = l; i <= r; i++) { b[cnt[a[i]]++] = a[i]; }
    for (i = l; i <= r; i++) { a[i] = b[i - l]; }
}
```
