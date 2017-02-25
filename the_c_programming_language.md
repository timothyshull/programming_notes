# Chapter 1 - A Tutorial Introduction
## Variables and Arithmetic Expressions
- declare variables before they are used
- declaration consists of a type and a name (optionally a list of names)
```
int fahr, celsius;
int lower, upper, step;
```
- main data types:
    - int
    - float
    - char
    - short
    - long
    - double
- assignment statements involve set a value to a declared variable
```
lower = 0;
```
- while loop - test condition, execute statements once per loop
```
while (fahr <= upper) {
       ...
}
```
- printf format specifiers:
    - %d - decimal
    - %6d - print decimal 6 characters wide
    - %f - print float
    - %6f - print float 6 characters wide
    - %.2f - print float with 2 characters after decimal
    - %6.2f - print float with 6 before 2 after
    
## The for statement
- for generalizes while with three parts in the condition
    - the initialization - performed once at beginning
    - loop control conditional
    - the increment (or decrement) step -> move towards finalization of the condition
    
## Symbolic Constants
- use a preprocessor #define
```
#define name replacement list
```
- any occurrences of the name afterwards (not in parens and not in another name)
  will be replaced
  
## Character Input and Output
- text stream - sequence of characters divided into lines
- use getchar to get the next character in a character stream
- use putchar to put a character to a stream
```
int c;
c = getchar();
while (c != EOF) {
    putchar(c);
    c = getchar();
}
```
- EOF is a constant defined in <stdio.h>
- simplified
```
int c;
while ((c = getchar()) != EOF) 
    putchar(c);
```
- != has a higher precedence than = so need the previous approach over
```
c = getchar() != EOF
// or
c = (getchar() != EOF)
```
- be aware of ASCII characters in reading characters like this
- review the character count, line count, and word count functions here

## Arrays
- declaration
```
int ndigit[10];
```
- subscript access starts at 0
```
ndigit[0] ... 
```
- try to keep if / elses flattened

## Functions
- aka subroutine or procedure - used to get tasks done in a modular way
- functions have form:
```
return-type function-name(parameter declarations, if any) {
    declarations
    statements 
}
```
- parameter in declaration (formal argument), actual argument or argument
  in call
- C assumes an int is returned by default if no declaration is available
- return can be omitted from main
- older C had a different function declaration format

## Arguments - Call by Value
- all function arguments are passed by value
- means a function is given a temporary copy of the passed arguments
- use pointers to modify an external object within a function

## Character Arrays
- character arrays that end with the null character '\0' are called (C) strings
- use the %s format specifier for C strings in printf calls

## External Variables and Scope
- variables declared in functions are local to the function (but may be 
  stored elsewhere)
- external variables can be defined in the global (outside of any function)
  scope or with the extern keyword
- if set, retain their value outside of the function that sets them
- avoid global variables