- Djikstra -> stacks and recursion

# factorial
```
unsigned int factorial(unsigned int n) {
    if (n == 0) {
        return 1;
    } else {
        return n * factorial(n - 1);
    }
}
```
- possible non-recursive implementation
```
unsigned int factorial(unsigned int n) {
    int t;
    int i;
    for (t = 1, i = 1; i <= n; i++) {
        t *= i;
    }
    return t;
}
```
- better
```
unsigned int factorial(unsigned int n) {
    int a = 1;
    for (int i = n; i > 1; i--) {
        a *= i;
    }
    return a;
}
```

# power
```
double power(double x, unsigned int n) {
    if (n == 0) {
        return 1.0;
    } else {
        return x * power(x, n - 1);
    }
}
```
- non-recursive
```
double power(double x, unsigned int n) {
    double a = 1.0;
    for (int i = n; i > 1; i--) {
        a *= x;
    }
    return a;
}
```

# tail recursion
- just a glorified loop
```
void tail(int i) {
    if (i > 0) {
        std::cout << i << " ";
        tail(i - 1);
    }
}
```
- iterative equivalent
```
void iterativeTail(int i) {
    for (; i > 0; i--) {
        std::cout << i << " ";
    }
}
```

# nontail recursion
- the call stack can store information without having to explicitly store it
```
void reverse() {
    char ch;
    std::cin.get(ch);
    if (ch != '\n') {
        reverse();
        std::cout.put(ch);
    }
}
```
- non-recursive
```
void iterativeReverse() {
    char stack[80];

    int ch;
    int i = 0;
    while (ch = (std::cin.get() && ch != '\n')) {
        stack[top] = ch;
        i++;
    }
    for (; i >=0; i--) {
        std::cout.put(stack[i]);
    }
}
```
- with separate stack
```
void nonRecursiveReverse() {
    int ch;
    while (ch = (std::cin.get() && ch != '\n')) {
        stack.push(ch);
    }
    while (!stack.empty()) {
        std::cout.put(stack.pop());
    }
}
```

# indirect recursion
- when one function calls another function that in turn calls the first (at some point in the ensuing call stack)
```
receive() -> decode() -> store() -> receive() -> decode() -> store()
```

# nested recursion
- function defined in terms of itself with the additional definition of parameters in terms of original function

# backtracking
- recursively travel all possible paths to find the correct solution
- relates to trying all possible permutations
- generically:
    - from starting point
        - test for accept or reject and return if either
        - while solution has not been found
            - recursively try each possibility in current path
            - if no solution, return to starting point
        - if no solution return false
- backtracking is really just depth-first search on an implicit graph (or tree)
```
```
```
void backtrack(candidate) {
    if (reject(instance, candidate)) {
        return;
    }
    if (accept(instance, candidate)) {
        return output(instance, candidate);
    }
    element s = first(instance, candidate);
    while (s != null_case) {
        backtrack(s);
        s = next(instance, s)l
    }
}

```

- recursive
```
boolean solve(Node n) {
    if n is a leaf node {
        if the leaf is a goal node, return true
        else return false
    } else {
        for each child c of n {
            if solve(c) succeeds, return true
        }
        return false
    }
}
```
- non-recursive
```
boolean solve(Node n) {
    put node n on the stack;
    while the stack is not empty {
        if the node at the top of the stack is a leaf {
            if it is a goal node, return true
            else pop it off the stack
        }
        else {
            if the node at the top of the stack has untried children
                push the next untried child onto the stack
            else pop the node off the stack

    }
    return false
}
```
# NOTE: review recursive descent parser here
