# linked lists
## head and tail conventions in linked lists
### circular, never empty
```
// first insert
head->next = head;

// insert t after x
t->next = x->next;
x->next = t;

// remove after x
x->next = x->next->next;

// traversal loop
t = head;
do {
    ...
    t = t->next;
} while (t != head);

// test for single item
if (head->next == head)
```

### head pointer, no tail
```
// initialize
head = nullptr;

// insert t after x
if (x == nullptr) {
    head = t;
    head->next = nullptr;
} else {
    t->next = x->next;
    x->next = t;
}

// remove after x
t = x->next;
x->next = t->next;

// traversal loop
for (t = head; t != nullptr; t = t->next)

// test for empty list
if (head == nullptr)
```

### dummy head node, no tail
```
// initialize
head = new node;
head->next = nullptr;

// insert t after x
t->next = x->next;
x->next = t;

// remove after x
t = x->next;
x->next = t->next;

// traversal loop
for (t = head->next; t != nullptr; t = t->next)

// test for empty list
if (head->next == nullptr)
```

### dummy head and tail nodes
```
// initialize
head = new node;
tail = new node;
head->next = tail;
tail->next = tail;

// insert t after x
t->next = x->next;
x->next = t;

// remove after x
x->next = x->next->next;

// traversal loop
for (t = head->next; t != tail; t = t->next)

// test for empty list
if (head->next == tail)
```


# list reversal
```
node t;
node y = head;
node r = nullptr;

while (y != nullptr) {
    t = y->next;
    y->next = r;
    r = y;
    y = t;
}

head = r;
```


# list sorts
to be completed


# string processing
## strlen(a)
```
int i;
for (i = 0; a[i] != '\0'; i++) ;
return i;

// pointers
b = a;
while (*b++) ;
return b - a - 1;
```

## strcpy(a, b)
```
int i;
for (i = 0; (a[i] = b[i]) != 0; i++);

// pointers
while (*a++ = *b++) ;
```

## strcmp(a, b)
```
for (i = 0; a[i] == b[i]; i++) {
    if (a[i] == '\0') {
        return 0;
    }
return a[i] - b[i];

// pointers
while (*a++ == *b++) {
    if (*(a - 1) == '\0') {
        return 0;
    }
}
return *(a - 1) - *(b - 1);
```

## strcat(a, b)
```
strcpy(a + strlen(a), b);
```


# review examples of multilists
- a linked list with more than one link per node with specific links supplying a 
  specific ordering (i.e. one for creation order, one for sort order)