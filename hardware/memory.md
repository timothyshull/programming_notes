- NOTE: written in 2007 so consider for design details
# Introduction
- computers and components used to be developed together (were simpler)
    - network and memory had comparable speed to CPU
- later, storage and memory subsystem performance fell below CPU performance significantly
    - mostly dealt with using software in OS
    - main memory
    - caches
- removing memory bottleneck occurs in following forms
    - RAM hardware design (speed and parallelism)
    - memory controller designs
    - CPU caches
    - direct memory access (DMA) for devices
- must understand design of commodity hardware to understand memory issues
- (uses Linux as OS context)

# Commodity Hardware Today
- horizontal scaling is more common than vertical scaling thus commodity hardware
  has overtaken specialized software
- standard data center focused around multicore (quad and more) CPUs (with hyper-threading
  in Intel chips)
    - quad core means 64 VCPUs
- large variance in commodity hardware design
- personal computers and smaller servers
```
        CPU1        CPU2
         V            V
         --------------  FSB (front side bus)
                V
     RAM <- Northbridge
                ^
                V
  PCI-E <-> Southbridge <-> SATA
                        <-> USB
```
- all CPUs connected to northbridge via front side bus (FSB)
- northbridge
    - memory controller (contained in northbridge)
        - implementation determines RAM used for machine
        - RAM
        - DRAM
        - Rambus
        - SDRAM
        - all require different memory controllers
- northbridge communicates with all other system devices via southbridge
- southbridge = I/O bridge
    - handles communication with devices via variety of buses
    - common
        - PCI
        - PCI-E (PCI Express)
        - SATA
        - USB
    - less common
        - PATA
        - IEEE 1394
        - serial
        - parallel
    - older systems attached AGP slots to northbridge for performace
- results of structure
    - all communication from one CPU to another must travel over the same bus used to
      communicate with the northbridge
    - all communication with RAM must pass through the northbridge
    - RAM has only a single port
    - communication between a CPU and a device attached to the southbridge is routed
      through the northbridge
    - bottlenecks
        - CPU access to RAM
            - can be (partially) resolved with DMA but creates contention for nrothbridge
              bandwidth
        - bus from northbridge to RAM
        - memory access waits are more prominent with multiple processors, cores, hyper-threads
- memory access patterns greatly influence performance
- some (more expensive) systems allow the northbridge to be attached to many external
  memory controllers
    - increases bandwidth
    - allows better concurrent access patterns
- other methods for increasing bandwidth
    - integrate memory controllers into the CPUs and attach memory to each CPU
        - AMD Opteron and SMP systems
        - related approach in Intel Nehalem with CSI (common system interface)
        - results in as many memory banks as CPUs
        - disadvantages
            - results in non-uniform memory access (NUMA)
            - interconnects between CPUs are used when memory attached to another
              processor is accessed which has a cost
- commodity NUMA machines are much more common now (2017)

## RAM Types
- why both SRAM and DRAM in the same machine?
    - SRAM is faster and provides same functionality
    - SRAM is also much more expensive

### Static RAM
- shows structure of 6 transistor SRAM cell
- one cell requires six transistors
- there are variants with four transistors but they have disadvantages
- maintaining the state of the cell requires constant power
- the cell state is available for reading almost immediately once the word access line WL
  is raised
- the signal is as rectangular (changing quickly between the two binary states) as
  other transistor-controlled signals
- the cell state is stable, no refresh cycles are needed
- there are other slower and less power hungry SRAM types available

### Dynamic RAM
- shows structure of typical DRAM cell here
- keeps state in capacitor and transistor is used to guard access to state
- with a capacitor, reading a cell discharges the capacitor
- the capacitor must be recharged at some point
- takes a short time for the capacity to dissipate, and leakage requires constant refreshes
- no access is possible during refresh phase, which may stall up to 50% of the
  memory accesses for some workloads
- the tiny charge in a cell requires a sense amplifier to read and distinguish between a 0 and a 1
- reading the cell causes the charge of the capacitor the be depleted so each read must
  be followed by a recharge
- charging and draining is not instantaneous
- various delays limit how fast DRAM can be
- the chip real estate needed for one DRAM cell is much smaller than for an SRAM cell
- SRAM cells need individual power for the transistors maintaining the state
- the cost wins (except in specialized hardware) which has serious implications for
  programmer

### DRAM Access
- process
    - program selects a memory location using a virtual address
    - processor translates to physical address
    - memory controller selects the RAM chip corresponding to that address
    - to select the individual memory cell on the RAM chip
        - parts of the physical address are passed on in the form of a number of address
          lines
        - address is passed encoded as a binary number using a smaller set of address lines
        - address passed to the DRAM chip this way must be demultiplexed first
        - a demultiplexer with N address lines will have 2N output lines
        - output lines can be used to select the memory cell
- size of a de-multiplexer increases exponentially with the number of input lines
  when speed is not to be sacrificed
- using an array the design can get by with one demultiplexer and one multiplexer of half the
  size, which results in a huge saving
- the row address selection (RAS) demultiplexer select the address lines of a whole
  row of cells
- when reading the content of all cells is made available to the column address selection
  (CAS) multiplexer
- content of one column is made available to the data pin of the DRAM chip
    - happens many times in parallel on a number of DRAM chips to produce a total number
      of bits corresponding to the width of the data bus
- for writing the new cell value is put on the data bus and when the cell is selected
  using the RAS and CAS it is stored in the cell
- many more complications and need specification for delay

### Conclusions
- there are reasons why not all memory is SRAM
- memory cells need to be individually selected to be used
- the number of address lines is directly responsible for the cost of the memory
  controller, motherboards, DRAM module, and DRAM chip
- it takes a while before the results of the read or write operation are available

## DRAM Access Technical Details
### Read Access Protocol
### Precharge and Activation
### Recharging
### Memory Types
### Conclusions
- accessing DRAM is not fast especially when compared with cache access and CPU speed

# Other Main Memory Users
- high-performance cards (network and mass-storage controllers) require DMA
    - can access memory both through northbridge and southbridge
- other buses, like USB, also require FSB bandwidth
    - even if they do not use DMA
- DMA helps but causes contention for FSB bandwidth
- many possibilities for configurations of hardware and memory
- some cheaper systems have graphics systems without separate RAM

# CPU Caches
- in 90's speed of CPU cores increased but memory bus and RAM performance did not increase
  proportionally
- very little very fast RAM loses out to a lot of relatively fast RAM
- computers can have SRAM and DRAM
    - OS would have to optimally distribute data to use SRAM if managing
    - SRAM serves as extension of the register set of the processor
    - SRAM is not managed by OS or user
    - transparently used by processors
- caching takes advantage of temporal and spatial locality (key to purpose/usefulness of
  caching)
    - for my laptop
        - L1 cache 64 KB per core
        - L2 cache 256 KB per core
        - L3 cache 4 MB to 24 MB shared (8 MB on my mac)
- cache management requires strict replacement and access policies

## CPU Caches in the Big Picture
- caches sit before CPU core in path to bus and main memory is on a separate path to bus
    - all loads and stores go through cache
- older systems stuck to von Neumann architecture
- L1 is two separate caches
    - L1i - instruction cache
    - L1d - data cache
- processors have multiple cores and each core can have multiple threads
    - separate cores have separate copies of (almost) all the hardware resources
    - cores can run completely independently unless they are using the same resources
    - threads share almost all of the processor’s resources
    - Intel’s implementation of threads has only separate registers for the threads and
      even that is limited, some registers are shared

## Cache Operation at High Level
- by default all data read or written by the CPU cores is stored in the cache
    - there are memory regions which cannot be cached but this is something only the OS
      implementers have to be concerned about
    - there are also instructions which allow the programmer to
      bypass certain caches
- if the CPU needs a data word the caches are searched first
    - the cache cannot contain the content of the entire main memory
    - each cache entry is tagged using the address of the data word in the main memory
    - a request to read or write to an address can search the caches for a matching tag
    - the address in this context can be either the virtual or physical address
      (based on the cache implementation)
- it is inefficient to chose a word as the granularity of the cache
- since neighboring memory is likely to be used together it is also be loaded into the
  cache together
- RAM modules are much more effective if they can transport many data words in a row
  without a new CAS or even RAS signal
- entries stored in the caches are "lines" of several contiguous words
- older sizes were 32 and 64 bytes
    - if the memory bus is 64 bits wide this means 8 transfers per cache line
- entire cache line is loaded into the L1d
    - the memory address for each cache line is computed by masking the address value
      according to the cache line size
    - for a 64 byte cache line this means the low 6 bits are zeroed
- when an instruction modifies memory the processor has to load a cache line first
  because no instruction modifies an entire cache line at once
    - exception: write-combining
    - not possible for a cache to hold partial cache lines
- a cache line which has been written to and which has not been written back to main memory
  is "dirty"
    - once written the dirty flag is cleared
- an eviction (making more room) from L1d pushes the cache line down into L2
    - room has to be made in L2 which might push the content into L3 and possibly then
      into main memory
    - each eviction is progressively more expensive
- this is model for exclusive caches on AMD processors
    -  Intel implements inclusive caches where each cache line in L1d is also present in L2
    - speeds eviction from L1d
    - disadvantage of wasting memory for content held in two places is minimal
- advantage of exclusive cache is that loading a new cache line only has to touch the
  L1d and not the L2, which could be faster
- CPUs manage the caches as they like as long as the memory model defined for the processor
  architecture is adhered to
  It is, for instance, perfectly fine for a processor to take advantage of little or no mem- ory
  bus activity and proactively write dirty cache lines back to main memory
- wide variety of cache architectures among the processors for the x86 and x86-64
    - facilitated by memory model abstraction
- in symmetric multi-processor (SMP) systems caches cannot work independently
    - "cache coherency" - all processors should see same memory content at all times
    - processors detect when another processor wants to read or write to a certain cache line
      rather than having access to the caches of other processors
- if a write access is detected and the processor has a clean copy of the cache, cache line is marked
  invalid
    - Future references require the cache line to be reloaded
    - read access on another CPU does not necessitate an invalidation
        - multiple clean copies can very well be kept around
- more sophisticated cache implementation
    - cache line is dirty in one processor’s cache and a second processor wants to read or
      write line
    - main memory is out-of-date and the requesting processor must get the cache line
      content from first processor
    - snooping -> first processor notices this situation and automatically sends the requesting
      processor the data
        - bypasses main memory
        - in some implementations the memory controller is supposed to notice this direct transfer
          and store the updated cache line content in main memory
- many cache coherency protocols
    - most important is MESI
        - a dirty cache line is not present in any other processor’s cache
        - clean copies of the same cache line can reside in arbitrarily many caches
    - If these rules can be maintained, processors can use their caches efficiently even
      in multi-processor systems.
- by following rules above, processors in multiprocessor systems need to monitor each
  others' write accesses and compare the addresses with those in their local caches
    - results in efficient cache use
- TODO: current timing estimates of caches accesses and evictions
- entire cost does not have to be paid for each occurrence of the cache load and miss
    - internal pipelines (of different lengths)
        - instructions are decoded and prepared for execution
        - part of the preparation is loading values from memory (or cache) if they are
          transferred to a register
        - if the memory load operation can be started early enough in the pipeline
          it may happen in parallel with other operations
        - the entire cost of the load may be hidden
        - often possible for L1d
        - possible for L2 for some processors with long pipelines
        - if CPU does not have sufficient resources for the memory access or final address
          of the load becomes available late as the result of another instruction the load
          costs cannot be hidden (completely)
- the CPU does not necessarily have to wait until the value is safely stored in memory
- for write operations
    - if execution of the following instructions appear to have the same effect as if the
      value were stored in memory
        - the CPU does not necessarily have to wait until the value is safely stored in memory
        - CPU can take shortcuts and start executing the next instruction early
        - shadow registers -> can hold values no longer available in a regular register
        - with shadow registers it is possible to change the value which is to be stored in the
          incomplete write operation

## CPU Cache Implementation Details
- cache must be able to handle each cell of main memory, with a small ratio of cache
  to main memory

### Associativity
- fully associative cache - each cache line can hold a copy of any memory location
    - processor core would have to compare the tags of each and every cache line with the
      tag for the requested address to access a cache line
    - tag would be comprised of the entire part of the address which is not the offset into
      the cache line


