https://www.gnu.org/software/libc/manual/html_node/String-and-Array-Utilities.html

# simple string processing
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
for (i = 0; (a[i] = b[i]) != 0; i++); // assignment like this returns the value of the char being assigned

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


# Strings (from Algorithms)
- uses:
    - information processing
    - genomics
    - communications systems
    - programming systems
- string characteristics:
    - characters
        - generally 2^16 (16 bit Unicode)
        - previously 7 bit or extended 8 bit Unicode
    - immutability (string literal pool)
    - indexing (0 indexed)
    - length
    - substrings in constant time (review how)
    - concatenation in linear time
    - character arrays
    - alphabets (collections of character sets)
        - binary
        - DNA
        - octal
        - decimal
        - hexadecimal
        - protein
        - lowercase
        - uppercase
        - base 64
        - ascii
        - extended ascii
        - unicode 16
    - numbers

## String Sorts
- two general methods:
    - LSD - examines characters from right-to-left
    - MSD - examines characters from left-to-right
- affected by the number of characters in the alphabet (256 for 8 bit, 65,536 for
  16 bit Unicode)

### Key-indexed counting
1. compute frequency counts
    - use keys of small integers
    - maintain and increment the index of the key + 1 in an array
    - use the counts as indices