# Chapter 2 - Stopping to Take a Look Around

# Chapter 3 - Inspecting and Setting Variables

# Chapter 4 - When a Program Crashes
## Memory Management
- most common crash -> illegal memory access
    - seg fault on unix-like platforms
    - general protection fault on Windows
    - occurs when hardware supports virtual memory and it must be in use
- VM layout
    - unix generalization
        - .text
            - instructions generated from code
            - includes statically linked code
        - .data
            - global and static variables
        - .bss
            - uninitialized global and static variables
        - heap
        - unused
        - stack
        - env
        - *dynamically linked code is included somewhere depending on platform
    - can view process memory layout on linux by viewing /proc/<PID>/maps file
- memory pages
    - defaults to 4096 on Pentium (older)
    - OS maintains a page table -> each process entries with
        - physical location of page on disk
        - permissions of page
    - no partial pages allocated
- page table accesses for running processes
    - global static variable access requires r/w access of data section
    - local variable access requires stack r/w access
    - function enter/exit requires stack access
    - malloc/new requires heap access
    - machine instructions require text section access

- TODO: more here
## Core files
- gcc -g -W -Wall <file>.c -o <outname>
- objdump -s core-file
- gdb <executable> <core-file> or gdb <executable> -c <core-file>
- gdb --args ./crash -p param1 -o param2

- creation
    - contains description of program's state when it died
        - contents of stack (or stacks for each thread)
        - CPU register contents
        - values of statically allocated and global variables
- overriding shell suppression
    - bash `ulimit -c unlimited` or size (`ulimit -c` to check)
    - tcsh/csh `limit coredumpsize 1000000`

- don't leave gdb while making code changes during debugging

# Chapter 6 - Debugging in a Multiple-Activities Context

# Chapter 7 - Other Tools
- use strace:
    - logs all system calls made by a program
- use ltrace:
    - logs all library calls made by the program

- splint -> static C secure code checking linter
- EFence -> dynamic memory checker
- GNU MALLOC_CHECK_ environment variable -> set 0 - 3
- GNU mcheck/mtrace function -> include mcheck.h, call mcheck/mtrace before heap functions

## Additional Notes
- prefer clang static analysis tools
- can use PVS studio on Linux
- for secure coding:
    - http://www.cert.org/secure-coding/tools/index.cfm
        - clang thread safety analysis
        - compiler-enforced buffer overflow elimination
        - rosecheckers
        - secure coding validation suite
        - As-If Infinitely Ranged Integer Model (checks integer overflow)
- valgrind
- memcheck
- https://github.com/DynamoRIO/dynamorio - suite of tools
