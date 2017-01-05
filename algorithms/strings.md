https://www.gnu.org/software/libc/manual/html_node/String-and-Array-Utilities.html

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