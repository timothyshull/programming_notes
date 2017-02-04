# Hash functions
- approximates equivalent likelihood that a key will hash to any
  of the m slots (uniformity)

## division method
- use remainder of k when divided by table size (modulo operation)
- best practice to avoid m as power of 2
- good to choose prime somewhere between (i.e. not too close to) powers of two

## multiplication method
- multiply key by constant in 0 < constant < 1
- extract bits of fractional component
- multiply by m (number of elements in table) and take the floor
- Knuth suggested (5^1/2 - 1)/2 as the constant
- Sedgewick suggests simplifying to (key * a) % m and using the golden ratio

## Prime numbers for hash tables
- Mersenne primes
    - all p = 2^t - 1 are prime for t = 2, 3, 5, 7, 13, 19, 31 and no other
      t < 31
- most efficient to use a lookup table

## Horner's algorithm

