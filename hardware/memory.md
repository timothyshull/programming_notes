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