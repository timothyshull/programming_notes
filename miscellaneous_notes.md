# Misc compiler
- object file - compiled file with placeholder addresses for externally defined functions/objects
- clang++ (g++) -c -o output.o input.cpp input2.o ...
- export PATH="///:$PATH" - make sure added directory is the parent if includes have directory in path name
- Mac - linking to a dynamic library export DYLD_LIBRARY_PATH="///:$DYLD_LIBRARY_PATH" (LD_LIBRARY_PATH)
- -c - only run preprocess, compile, and assemble steps
- -F - add directory to framework include search path
- -I - add directory to include search path
- -L - pass dirrecotry to search for libraries to the linker (g++)
- -l - specify the name of a library to include (g++)
- -Wl - clang linker arguments
- -dynamiclib - mac command to create dynamic library,
- -shared - command to create dynamic libraries for other platforms
- ar ru - create static library with archiver tool
- ranlib - combined with ar to create static libraries - updates the library object files and index
- can use libtool in place of ar ru & ranlib
- use otool to display information about object files
- use -install_name on mac when creating a dylib to avoid having to export DYLD_LIBRARY_PATH
- clang++ -S -mllvm --x86-asm-syntax=intel test.cpp

- run C source code through preprocessor
```
cpp main.c main.i
clang -E main.c > main.i
```
- preprocessed C source code -> Assembly
```
gcc -S main.i
clang -S main.i
```
- assembly -> Relocatable object file
```
gcc -c main.s
clang -c main.s
```
- link object files and produce executable object file
```
gcc -o main main.o header.o
clang main.o header.o
```
- output the symbol table of an object file
```
readelf --symbols main.o
```
- output the ELF headers
```
readelf -h main.o
```
- if entry point address is 0x0, object file has no ‘main’
```
readelf -h main.o | grep "Entry point address"
```

## Additional Commands
- tail -f /private/var/log/system.log (macOS)
- tail -f /var/system.log (macOS & Unix)
- ar
- ranlib
- nm
- libtool
- otool
- size
- leaks
- malloc_history

# Disassemblers
- Hopper - https://sites.fastspring.com/hopperapp/product/hopperdisassemblerv3
- objdump
- gdb
- ndisasm
- llvm-objdump
- llvm-mc
- otool
- capstone
```
llvm-dis -c .o file
```


# Misc Networking
- sample output of ifconfig
```
eno16777736: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>
mtu 1500
inet 172.16.128.136
netmask 255.255.255.0
broadcast 172.16.128.255
inet6 fe80::20c:29ff:fecf:6b5f
prefixlen 64
scopeid 0x20<link>
ether 00:0c:29:cf:6b:5f
txqueuelen 1000  (Ethernet)
RX packets 103282  bytes 145463398 (138.7 MiB)
RX errors 0  dropped 0  overruns 0  frame 0
TX packets 48322  bytes 3426769 (3.2 MiB)
TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

###

en0: flags=8863<UP,BROADCAST,SMART,RUNNING,SIMPLEX,MULTICAST>
mtu 1500
ether 14:10:9f:d3:42:f1
inet 192.168.1.2
netmask 0xffffff00
broadcast 192.168.1.255
media: autoselect
status: active
```
- gateway - address of gateway (computer) to outside of LAN
    - only one ipv4 and one ipv6 gateway should be set and this is the point for
      communication beyond the LAN with other computers (starts at router, but includes  NAT and firewall)
- broadcast - address used to communicate with all of the computers on the LAN
- name resolution order - /etc/nsswitc.conf, /etc/hosts, /etc/resolv.conf is used for DNS
- /etc/hosts
    - main purpose of /etc/hosts configuration file is to resolve hostnames that cannot be resolved any other way
    - can also be used to resolve hostnames on small networks with no DNS server
    - regardless of the type of network the computer is on, this file should contain a line specifying the IP address
      of the loopback device (127.0.0.1) as localhost.localdomain.
- /etc/resolv.conf
    - /etc/resolv.conf configuration file specifies the IP addresses of DNS servers and the search domain
      Unless configured to do otherwise, the network initialization scripts populate this file
- /etc/sysconfig/network
    - /etc/sysconfig/network configuration file specifies routing and host information for all network interfaces.

- /etc/sysconfig/network-scripts/ifcfg-<interface-name> (different for different flavors of Linux)
    - for each network interface, there is a corresponding interface configuration script
    - each of these files provide information specific to a particular network interface

# Parallel Libs/Frameworks
- cilk plus -> install with custom gcc branch or custom llvm branch
    - https://gcc.gnu.org/git/gcc.git/
    - https://cilkplus.github.io/
- threading building blocks - brew install tbb
- openmp - brew install gcc —-without-multilib
- cuda - download installer from nvidia
- opencl - https://developer.apple.com/opencl/
    - included on mac OS after 10.7, integrates with GCD
    - https://developer.apple.com/library/content/documentation/Performance/Conceptual/OpenCL_MacProgGuide/Introduction/Introduction.html
- opengl - https://developer.apple.com/opengl/
- macOS Frameworks
    - Accelerate
    - Metal
    - OpenCL
    - OpenGL

    - NetFS
    - GLUT
    - GlKit


# Essential Tests for a System
- rebooting host (bare minimum)
- max out CPU
- take up memory and force garbage collection
- consume bandwidth to disk and cause wait for I/O
- network
    - dependency slow down
    - dependency disappears
    - packet loss
    - corruption
