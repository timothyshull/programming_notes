# Cormen Ch. 3 - Growth of Functions
- running time growth = asymptotic efficiency
- interested in input size vs. output size as it approaches a limit when
  the input is increased without bound

## Asymptotic notation
- input domain is natural numbers N = {0, 1, 2, 3, ...}
- sometimes extended to real numbers R = {..., -2, -1, 0, 1, 2, ...}

### Asymptotic notation, functions, and running times
- generally reduce to largest element for running time
- can be used to characterize memory usage and other algorithmic details

### O(theta)-notation
- for a given function, g(n), denote O(g(n)) as the set of functions:
    O(g(n)) = {f(n): for positive constants c1, c2, and n0 such that
                     0 <= c1*g(n) <= f(n) <= c2*g(n) for all n >= n0}
- this means that the O(theta)-descriptive function should always bound
  f(n) between c1*g(n) and c2*g(n)
- f(n) is equal to g(n) to within a constant factor, i.e. g(n) is an
  asymptotically tight bound
- f(n) must be asymptotically non-negative, i.e. positive for sufficiently
  large n
- asymptotically positive - positive for all sufficiently large n

### O(omicron)-notation
- O(theta) bounds from above and below
- O(omicron) is used when there is only an asymptotic upper bound
- for a given function, g(n), denote O(g(n)) as the set of functions:
    O(g(n)) = {f(n): for positive constants c and n0 such that
                     0 <= f(n) <= c*g(n) for all n >= n0}