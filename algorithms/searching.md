# Symbol Tables and Binary Search Trees
- symbol table: a data structure that supports insert and get of an item using a key

## Symbol-Table Abstract Data Type
- operations:
    - insert
    - search (from key)
    - remove
    - select the k-th largest (smallest) item
    - sort
    - join two symbol tables
- C++ standard has bsearch
- item ADT for symbol table:
```
#include <stdlib.h>
#include <iostream.h>
static int maxKey = 1000;
typedef int Key;
class Item {
private:
    Key keyval;
    float info;
public:
    Item() { keyval = maxKey; }
    Key key() { return keyval; }
    int null() { return keyval == maxKey; }
    void rand() {
        keyval = 1000*::rand()/RAND_MAX;
        info = 1.0*::rand()/RAND_MAX;
    }
    int scan(istream& is = cin) { return (is >> keyval >> info) != 0; }
    void show(ostream& os = cout) { os << keyval << " " << info << endl; }
};

ostream& operator<<(ostream& os, Item& x) {
    x.show(os); return os;
}
```
- also want operators == and <
- symbol table ADT:
```
template<typename Item, typename Key>
class Symbol_table {
private:
    // Implementation-dependent code
public:
    ST(int);
    int count();
    Item search(Key) ;
    void insert(Item);
    void remove(Item);
    Item select(int);
    void show(ostream&);
};
```
- also need appropriate definitions for Key, key(), null(), operator== and operator<
- sorted and unsorted symbol tables (if a key does not support operator<, select and sort are generally not supported)
- symbol tables with duplicate keys and symbol tables with only unique keys
- for duplicate keys:
    - only distinct keys and each item structure for a key maintains a list of values
    - leave all duplicates in the data structure and return ANY item with key
    - assume each item has a separate unique ID and find the item based on that and the key
- sample client:
```
#include <iostream.h>
#include <stdlib.h>
#include "Item.h"
#include "Symbol_table.h"

int main(int argc, char *argv[]) {
    int N, maxN = atoi(argv[1]), sw = atoi(argv[2]);
    Symbol_table<Item, Key> st(maxN);
    for (N = 0; N < maxN; N++) {
        Item v;
        if (sw) v.rand(); else if (!v.scan()) break;
        if (!(st.search(v.key())).null()) continue;
        st.insert(v);
    }
    st.show(cout);
    cout << "\n";
    cout << N << " keys" << endl;
    cout << st.count() << " distinct keys" << "\n";

    return 0;
 }
 ```
 - additional operations:
    - memoized "finger" search
    - range search
    - distance between keys
    - near-neighbor search

## Key-Indexed Search
- using indices of distinct small numbers enables a key-indexed array based symbol table:
```
template <typename Item, typename Key>
class Symbol_table {
private:
    Item nullItem, *st;
    int M;
public:
    Symbol_table(int maxN) {
        M = nullItem.key();
        st = new Item[M];
    }
    int count() {
        int N = 0;
        for (int i = 0; i < M; i++) {
            if (!st[i].null()) {
                N++;
            }
        }
        return N;
    }
    void insert(Item x) { st[x.key()] = x; }
    Item search(Key v) { return st[v]; }
    void remove(Item x) { st[x.key()] = nullItem; }
    Item select(int k) {
        for (int i = 0; i < M; i++) {
            if (!st[i].null()) {
                if (k-- == 0) {
                    return st[i];
                }
            }
        }
        return nullItem;
    }
    void show(ostream& os) {
        for (int i = 0; i < M; i++) {
            if (!st[i].null()) {
                st[i].show(os);
            }
        }
    }
};
```
- direct corollary to key-indexed sort
- method of choice when applicable (possible) due to efficiency
- when only keys, can use a table of bits aka existence table
- properties:
    - for distinct positive integer keys less than M, key-indexed search
      can be implemented with search, insert, and remove running in constant
      time and initialize, select, and sort running in time proportional to M
- for duplicate keys, can use linked lists as elements in the array
- count here is lazy; can make it eager by maintaining a counter of the non-empty
  positions -> prefer the lazy approach if the use of count is rare

## Sequential Search
- for keys from too large a range to be used as indices it is possible to still use
  a similar array based approach by storing the items in order and inserting a new
  item in sorted order
```
template<typename Item, typename Key>
class Symbol_table {
private:
    Item nullItem, *st;
    int N;
public:
    Symbol_table(int maxN) {
        st = new Item[maxN+1]; N = 0;
    }
    int count() { return N; }
    void insert(Item x) {
        int i = N++;
        Key v = x.key();
        while (i > 0 && v < st[i-1].key()) {
            st[i] = st[i-1];
            i--;
        }
        st[i] = x;
    }
    Item search(Key v) {
        for (int i = 0; i < N; i++) {
            if (!(st[i].key() < v)) {
                break;
            }
        }
        if (v == st[i].key()) {
            return st[i];
        }
        return nullItem;
    }
    Item select(int k) { return st[k]; }
    void show(ostream& os) {
        int i = 0;
        while (i < N) st[i++].show(os);
    }
};
```
- can improve inner loop in search by maintaining a sentinel to remove the overflow check
- here search is fast, but insert is slow
- can instead not maintain the array in sorted order and insert is fast but search and sort are slow
- linked list version (unordered):
```
#include <stdlib.h>
template<typename Item, typename Key>
class Symbol_table {
private:
    Item nullItem;
    struct node {
        Item item;
        node* next;
        node(Item x, node* t) {
            item = x;
            next = t;
        }
    };
    typedef node *link;
    int N;
    link head;
    Item searchR(link t, Key v) {
        if (t == 0) {
            return nullItem;
        }
        if (t->item.key() == v) {
            return t->item;
        }
        return searchR(t->next, v);
    }
public:
    Symbol_table(int maxN) {
        head = 0;
        N = 0;
    }
    int count() { return N; }
    Item search(Key v) { return searchR(head, v); }
    void insert(Item x) {
        head = new node(x, head);
        N++;
    }
};
```
- properties:
    - sequential search in an N item symbol table uses N/2 comparisons on average
      (array or linked lists, ordered or unordered)
    - uses a constant number of steps for inserts and N comparisons for search misses (always)
    - uses N/2 comparisons for insertions, search hits, and search misses on average

## Binary Search
- binary search
    - split input in half, compare to find the portion the key belongs to, repeat
- properties:
    - never uses more than lg(n) + 1
    - (the number of comparisons is equal to the number of bits in the binary representation of N)
    - sorting a table causes a slowdown for insert
- binary search for an array based symbol table
```
private:
    Item searchR(int l, int r, Key v) {
        if (l > r) return nullItem;
        int m = (l+r)/2;
        if (v == st[m].key()) return st[m];
        if (l == r) return nullItem;
        if (v < st[m].key()) {
            return searchR(l, m-1, v);
        } else {
            return searchR(m+1, r, v);
        }
    }
public:
    Item search(Key v) { return searchR(0, N-1, v); }
```
- for binary search with a given input, the sequence is predetermined
- improvements:
    - guess first comparison with more precision (interpolation search)
      (assumes key value is is numerical and evenly distributed)
```
int m = (l + r) / 2;
// ->
int m = l + (v - a[l].key()) * (r - l) / a[r[.key() - a[l].key());
```

## Binary Search Trees (BSTs)
- using a tree overcomes the cost of insertion in a sorted implementation for binary sort
- (this is one of the most fundamental algorithms in computer science)
- definition:
    - binary search tree: a tree with keys associated with each node and the
      the keys in the left subtree of each node is less than the key at the node
      and the keys in the right subtree of each node is greater than the key at the node
- binary search tree symbol table
```
template<typename Item, typename Key>
class Symbol_table {
private:
    struct node {
        Item item;
        node *l, *r;
        node(Item x) {
            item = x;
            l = 0;
            r = 0;
        }
    };
    typedef node *link;
    link head;
    Item nullItem;
    Item searchR(link h, Key v) {
        if (h == 0) return nullItem;
        Key t = h->item.key();
        if (v == t) {
            return h->item;
        }
        if (v < t) {
            return searchR(h->l, v);
        } else {
            return searchR(h->r, v);
        }
    }
    void insertR(link& h, Item x) {
        if (h == 0) {
            h = new node(x);
            return;
        }
        if (x.key() < h->item.key()) {
            insertR(h->l, x);
        } else {
            insertR(h->r, x);
        }
    }
public:
    Symbol_table(int maxN) { head = nullptr; }
    Item search(Key v) { return searchR(head, v); }
    void insert(Item x) { insertR(head, x); }
};
```
- can also use dummy nodes
- sorting with a BST:
```
private:
void showR(link h, ostream& os) {
    if (h == nullptr) {
        return;
    }
    showR(h->l, os);
    h->item.show(os);
    showR(h->r, os);
}
public:
    void show(ostream& os) { showR(head, os); }
```
- non-recursive BST insertion
```
void insert(Item x) {
    Key v = x.key();
    if (head == nullptr) {
        head = new node(x);
        return;
    }
    link p = head;
    for (link q = p; q != 0; p = q ? q : p) {
        q = (v < q->item.key()) ? q->l : q->r;
    }
    if (v < p->item.key()) {
        p->l = new node(x);
    } else {
        p->r = new node(x);
    }
}
```
- the non-recursive BST operations require maintaining a link to the parent of the current
  node
- duplicate keys can be used in BSTs but will result in scattered positioning throughout the tree

## Performance Characteristics of BSTs
- dependent on shapes of trees
- height is worst case costs
- properties:
    - search hits require 2 * ln(N) or about 1.39 * lg(N) on average from N random keys
    - insertions and search misses require about 2 * ln(N) or about 1.39 * lg(N) comparisons on average
    - in the worst case a search in a binary search tree with N keys can require N comparisons
- a recurrence relationship exists that describes the number of comparisons and internal path length

## Index Implementations with Symbol Tables
