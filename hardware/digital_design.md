# Ch. 1 - From Zero to One
## 1.2 The Art of Managing Complexity
### Abstraction
- levels of abstraction for an electronic computing system
    - application software
    - operating systems
    - microarchitecture
    - logic
    - digital circuits
    - analog circuits
    - devices
    - physics
- hiding details when they are not important
- systems can be viewed from many different levels of abstraction
- electronic devices
    - terminals - connection points in electronic systems
- analog circuits
- digital circuits
- microarchitecture - links the logic and architecture levels of abstraction
- architecture - level of abstraction that describes a computer from the programmer’s perspective
- operating system

### Discipline
- the act of intentionally restricting your design choices so that you can work more productively at a higher level of
  abstraction
- using interchangeable parts is an application of discipline
- digital discipline sets context
    - allows for combination of components

### The Three -Y's
- hierarchy
    - dividing a system into modules, then further subdividing each of these modules until
      the pieces are easy to understand
- modularity
    - states that the modules have well-defined functions and interfaces, so that they
      connect together easily without unanticipated side effects
- regularity
    - seeks uniformity among the modules
    - common modules are reused many times, reducing the number of distinct modules that
      must be designed

## 1.3 The Digital Abstraction
- Digital systems
    - represent information with discrete-valued variables
        - variables with a finite number of distinct values
- Babbage's machine (1870's) used 0 - 9 with 25 rows of gears, or 25 digit precision
- amount of information D in a discrete valued variable with N distinct states is measured in units of bits as
  D = log2N bits
- bit - Binary digIT
- boolean logic - George Boole (mid 1800's)
- digital abstraction allows designers to not be concerned with physical medium of digital
  signal generation

## 1.4 Number Systems
### Decimal Numbers
- 0 - 9 (base 10)
- right to left column weights of 10^column (0 indexed)
- n-digit number represents one of 10^n possible numbers

### Binary Numbers
- one of two values (0 or 1, true or false, etc)
- base 2
- an n-bit binary number represents one of 2^n possibilities

### Hexadecimal Numbers
- group of four bits represents one of 2^4 = 16 possibilities
- base 16
- 0-9 A-F

### Bytes, Nibbles, and All That Jazz
- byte - group of 8 bits
- nibble - group of four bits
- words - data chunk handled by microprocessor, dependent on architecture
- least significant bit - bit in 1's column
- most significant bit - bit at opposite end of lsb
- least and most significant bytes in words
- kilo - 2^10 bytes
- mega - 2^20
- giga - 2^30
- remember powers of 2

### Binary addition
- like decimal addition with carries of 1 from column to column
- digital systems usually operate on a fixed number of digits
- overflow - for addition, if the result is too big to fit in the available digits

### Signed Binary Numbers
#### Sign/Magnitude
- msb is sign bit and remainder is magnitude
- ordinary binary addition does not work for sign/magnitude numbers
- an n-bit sign/magnitude number spans the range −2^n−1 + 1, 2^n−1 − 1
- both +0 and −0 exist

#### Two's Complement Numbers
- identical to unsigned binary numbers except that the most significant bit position has a weight of
  −2^n−1 instead of 2^n−1
- single 0 representation and addition work properly for both positive and negative numbers
- 0 -> all 0's
- greatest positive value -> 0 and all 1's
- lowest negative value -> 1 and all 0's
- -1 -> all 1's
- to reverse sign (take two's complement)
    - invert all bits
    - add 1
- to subtract - take two's complement of second operand and then add
- 0 is considered positive because it's sign bit is 0
- n-bit two’s complement numbers represent one of 2^n possible values
- overflow may occur
    - overflow occurs if the two numbers being added have the same sign bit and the result has the opposite sign bit

#### Comparison of Number Systems
- three most common are unsigned, two's complement, and sign/magnitude
- ranges
    - unsigned - 0, 2^n - 1
    - sign/magnitude - -2^(n-1) + 1, 2^(n-1) - 1
    - two's complement
- two’s complement numbers are convenient because they represent both positive and negative integers and because
  ordinary addition works for all numbers
- unless stated otherwise, assume that all signed binary numbers use two’s complement representation

## 1.5 Logic Gates
- simple digital circuits that take one or more binary inputs and produce a binary output
- relationship between input and output can be described with a truth table or boolean
  equation
- single input gates
    - NOT
    - buffer
- two input gates
    - AND
        - 00->0
        - 01->0
        - 10->0
        - 11->1
    - OR
        - 00->0
        - 01->1
        - 10->1
        - 11->1
    - XOR
        - 00->0
        - 01->1
        - 10->1
        - 11->0
    - NAND
        - 001
        - 011
        - 101
        - 110
    - NOR
        - 001
        - 010
        - 100
        - 110
    - XNOR
        - 001
        - 010
        - 100
        - 111

### Multiple Input Gates
- n-input AND gate - true when all inputs are true
- n-input OR gate - true when at least 1 input is true
- 3-input NOR gate - output true only if 1 of the inputs is true

## 1.6 Beneath the Digital Abstraction
- a digital system uses discrete-valued variables but the variables are represented by continuous physical quantities

### Supply Voltage
- lowest voltage in the system is 0 V, also called ground or GND
- highest voltage in the system comes from the power supply and is usually called VDD
- in 1970’s and 1980’s technology, VDD was generally 5V
- as chips have progressed to smaller transistors, VDD has dropped to 3.3 V, 2.5 V, 1.8 V, 1.5 V, 1.2 V
    - to save power and avoid overloading the transistors

### Logic Levels
- used to map continuous variables onto a discrete binary variable
- first gate is driver -> second gate is receiver
- The driver produces a LOW (0) output in the range of 0 to VOL or a HIGH(1) output in the range of VOH to VDD
- if the receiver gets an input in the range of 0 to VIL, it will consider the input to be LOW
- if the receiver gets an input in the range of VIH to VDD, it will consider the input to be HIGH
- if the receiver’s input should fall in the forbidden zone (VIL -> VIH) gate behavior is unpredictable
- output and input high and low logic levels

### Noise Margins
- the amount of noise that could be added to a worst-case output such that the signal can still be interpreted as a
  valid input

### DC Transfer Characteristics
- DC transfer characteristics of a gate describe the output voltage as a function of the input voltage when the input
  is changed slowly enough that the output can keep up
- called transfer characteristics because they describe the relationship between input and output
  voltages
- a reasonable place to choose the logic levels is where the slope of the transfer characteristic dV(Y) / dV(A) is −1
  (two points are called the unity gain points)

### Static Discipline
- requires that, given logically valid inputs, every circuit element will produce logically valid outputs
- trade freedom of using arbitrary analog circuit elements in return for the simplicity and robustness of
  digital circuits
- logic families - grouping of gates such that all gates in a logic family obey the static
  discipline when used with other gates in the family
- four major families
    - Transistor-Transistor Logic (TTL)
    - Complementary Metal- Oxide-Semiconductor Logic (CMOS)
    - Low Voltage TTL Logic (LVTTL)
    - Low Voltage CMOS Logic (LVCMOS)

## 1.7 CMOS Transistors
- transistors - electrically controlled switches that turn ON or OFF when a voltage or current
  is applied to a control terminal
- two main types of transistors
    - bipolar junction transistors
    - metal-oxide-semiconductor field effect transistors (MOSFETs or MOS transistors)
- 1958 - Jack Kilby at Texas Instruments built the first integrated circuit containing two transistors
- 1959 - Robert Noyce at Fairchild Semiconductor patented a method of interconnecting multiple transistors on a single
  silicon chip

### Semiconductors
- MOS transistors are built from silicon
    - silicon (Si) has four electrons in its valence shell and forms bonds with four
      adjacent atoms, resulting in a crystalline lattice
    - becomes a better conductor when small amounts of impurities, called dopant atoms, are carefully added
- n-type dopant - the electron carries a negative charge
- hole
- p-type dopant
- semiconductor - name given to class of conductors when conductivity changes over many orders of magnitude depending
  on the concentration of dopants

### Diodes
- diode - a junction between an p-type and n-type silicon
- cathode - n-type region
- anode - p-type region
- forward biased - voltage on anode is higher
- reverse biased - voltage on cathode is higher

### Capacitors
- capacitor - consists of two conductors separated by an insulator
    - when a voltage V is applied to one of the conductors, the conductor accumulates electric charge Q and
      the other conductor accumulates the opposite charge −Q
- capacitance C of the capacitor is the ratio of charge to voltage: C = Q/V
    - proportional to the size of the conductors and inversely proportional to the distance
      between them

### nMOS and pMOS Transistors
- MOSFET is a sandwich of several layers of conducting and insulating materials, built on thin flat wafers of silicon
    - created by sequence of steps in which dopants are implanted into the silicon, thin films of
      silicon dioxide and silicon are grown, and metal is deposited
    - between each step, the wafer is patterned so that the materials appear only where they are desired
- once complete, the wafer is cut into rectangles called chips or dice that contain thousands, millions, or even
  billions of transistors
- chip is then placed in a plastic or ceramic package with metal pins to connect it to a
  circuit board
- n-type transistors, called nMOS, have regions of n-type dopants adjacent to the gate called the source and
  the drain and are built on a p-type semiconductor substrate
- pMOS transistors are just the opposite, consisting of p-type source and drain regions in an n-type substrate
- MOSFET behaves as a voltage-controlled switch in which the gate voltage creates an
  electric field that turns ON or OFF a connection between the source and drain
- name field effect transistor comes from this principle of operation
- description of nMOS and pMOS functionality here

### CMOS NOT Gate
### Other CMOS Logic Gates
### Transmission Gates
- nMOS transistors are good at passing 0 and pMOS transistors are good at passing 1
    - parallel combination of the two passes both values well
- such a circuit is called a transmission gate

### Pseudo-nMOS Logic
- transistors in series are slower than transistors in parallel
- resistors in series have more resistance than resistors in parallel
- pMOS transistors are slower than nMOS transistors because holes cannot move around the silicon
  lattice as fast as electrons
- parallel nMOS transistors are fast
- series pMOS transistors are slow especially when many are in series
- pseudo-nMOS logic replaces the slow stack of pMOS transistors with a single weak pMOS transistor that is always ON
    - often called a weak pull-up
    - can be used to build fast NOR gates with many inputs
    - useful for certain memory and logic arrays
    - disadvantage is that a short circuit exists between VDD and GND
        - when the output is LOW or the weak pMOS and nMOS transistors are both ON
        - the short circuit draws continuous power
        - pseudo-nMOS logic must be used sparingly

## 1.8 Power Consumption
- amount of energy used per unit time
- very important
- digital systems draw both dynamic and static power
- dynamic power is the power used to charge capacitance as signals change between 0 and 1
- static power is the power used even when signals do not change and the system is idle

# Ch.2 - Combination Logic Design
## 2.1 Introduction
- digital circuit - a network that processes discrete-valued variables
    - black box with
        - one or more discrete-valued input terminals
        - one or more discrete-valued output terminals
        - a functional specification describing the relationship between inputs and outputs
        - a timing specification describing the delay between inputs changing and outputs responding
    - composed of nodes and elements
- element - a circuit with inputs, outputs, and a specification
- node - a wire, whose voltage conveys a discrete-valued variable
    - input
    - output
    - internal
- combinational or sequential
- combinational circuit - outputs depend only on the current values of the inputs
    - combines the current input values to compute the output
- sequential circuit - outputs depend on both current and previous values of the inputs
    - depends on the input sequence
- a combinational circuit is memoryless
- a sequential circuit has memory
- combination composition rules
    - every circuit element is itself combinational
    - every node of the circuit is either designated as an input to the circuit or connects to exactly one output
      terminal of a circuit element
    - the circuit contains no cyclic paths
        - every path through the circuit visits each circuit node at most once
- certain circuits that disobey these rules are still combinational, so long as the
  outputs depend only on the current values of the inputs
    - making this determination is difficult
- the functional specification of a combinational circuit is usually expressed as a truth table or a Boolean equation

## 2.2 Boolean Equations
- boolean algebra is used to simplify boolean equations
- axioms
    - A1 - `B = 0 if B != 1`
    - A2 - `complement of 0 = 1`
    - A3 - `0 • 0 = 0`
    - A4 - `1 • 1 = 1`
    - A5 - `0 • 1 = 1 • 0 = 0`
- duals
    - A1' - `B = 1 if B != 0`
    - A2' - `complement of 1 = 0`
    - A3' - `1 + 1 = 1`
    - A4' - `0 + 0 = 0`
    - A5' - `1 + 0 = 0 + 1 = 1`
- names
    - 1 - binary field
    - 2 - not
    - 3 - and/or
    - 4 - and/or
    - 5 - and/or
- duality - if the symbols 0 and 1 and the operators • (AND) and + (OR) are interchanged the statement will still be
  correct

### Axioms
### Theorems of One Variable
- identity
    - B AND 1 = B
    - dual - B OR 0 = B
- null element theorem
    - B AND 0 = 0
    - dual - B OR 1 = 1
- 0 is null for AND
- 1 is null for OR
- idempotency
    - B AND B = B
    - dual - B OR B = B
- involution (complementing a variable twice results in original variable
    - complement of complement of B = B
- complements
    - B AND complement of B = 0
    - dual - B OR complement of B = 1

### Theorems of Several Variables
- TODO: finish table here
- commutativity
    - T6 - B • C = C • B
    - T6' - Β + C = C + Β
- associativity
    - T7 - (Β • C) • D = Β • (C • D)
    - T7' - (B + C) + D = B + (C + D)
- distributivity
    - T8 - (Β • C) + (Β • D) = Β • (C + D)
    - T8' - (B + C) • (B + D) = B + (C • D)
- covering
    - T9 - Β • (Β + C) = Β
    - T9' - B + (B • C) = B
- combining
    - T10 - (Β • C) + (B • C) = Β
    - T10' - (B + C) • (B + C) = B
- consensus
    - T11 - (Β • C) + (B • D) + (C • D) = B • C + B • D
    - T11' - (B + C) • (B + D) • (C + D) = (B + C) • (B + D)
- De Morgan's Theorem
    - T12 - conjugate of B0 • B1 • B2... = B0 + B1 + B2...
    - T12' - conjugate of B0 + B1 + B1... = B0 • B1 • B2... 

- De Morgan's Theorem
    - complement of the product of all the terms is equal to the sum of the complement of each
      term
    - complement of the sum of all the terms is equal to the product of the complement of
      each term
- rules for bubble pushing
    - pushing bubbles backward (from the output) or forward (from the inputs) changes the body of the gate from AND to
      OR or vice versa
    - pushing a bubble from the output back to the inputs puts bubbles on all gate inputs
    - pushing bubbles on all gate inputs forward toward the output puts a bubble on the output

### The Truth Behind it All
- proof for theorems with a finite number of variables
    - show that the theorem holds for all possible values of these variables
    - this method is called perfect induction
    - can be done with a truth table

### Simplifying Equations
- an equation in sum-of-products form is minimized if it uses the fewest possible implicants
    - if there are several equations with the same number of implicants, the minimal one is the one with the
      fewest literals
- an implicant is called a prime implicant if it cannot be combined with any other
  implicants in the equation to form a new implicant with fewer literals
    - the implicants in a minimal equation must all be prime implicants
    - otherwise, they could be combined to reduce the number of literals
- expanding an implicant is sometimes useful in minimizing equation

## 2.4 From Logic to Gates
- schematic - diagram of a digital circuit showing the elements and the wires that connect them together
- programmable logic array (PLA) - inverters, AND gates, and OR gates are arrayed in a systematic fashion

## 2.5 Multilevel Combinational Logic
- two-level logic - logic in sum-of-products form that consists of literals connected to a level of AND gates connected
  to a level of OR gates

### Hardware Reduction

- TODO: more here


# Ch. 3 - Sequential Logic Design
## 3.2 Latches and Flip-Flops
- bistable element - element with two stable states; fundamental building block of memory
- connecting pair of inverters in loop leads to simple bistable element
    - additional third state with both outputs halfway between
    - called a metastable state
- an element with N stable states conveys log2N bits of information
    - bistable element stores one bit
    - state of the cross-coupled inverters is contained in one binary state variable, Q
    - value of Q tells everything about the past that is necessary to explain the future behavior
        - if Q = 0, it will remain 0 forever
        = if Q = 1, it will remain 1 forever
    - circuit has another node, Q hat
    - Q hat does not contain any additional information because if Q hat is known, Q is also known

### SR Latch
- composed of two cross-coupled NOR gates; one of the simplest sequential circuits
    - state can be controlled through two inputs, S and R, which can set and reset the output Q
- steps through input possibilities here

### D Latch
- SR latch behaves strangely when both S and R are simultaneously asserted
    - conflates what and when
- D latch solves these problems
    - data input, D, controls what the next state should be
    - clock input, CLK, controls when the state should change
clock controls when data flows through the latch
    - when CLK = 1, the latch is transparent
        - data at D flows through to Q as if the latch were just a buffer
    - when CLK = 0, the latch is opaque
        - blocks the new data from flowing through to Q
        - Q retains the old value
- D latch is sometimes called a transparent latch or a level-sensitive latch
- D latch updates its state continuously while CLK = 1

### D Flip-Flop
- can be built from two back-to-back D latches controlled by complementary clocks
    - master and slave
- IMPORTANT!!!! D flip-flop copies D to Q on the rising edge of the clock, and remembers its state at all other times
    - rising edge of the clock is often called the clock edge
    - D input specifies what the new state will be
    - clock edge indicates when the state should be updated
- also known as
    - master-slave flip-flop
    - edge-triggered flip-flop
    - positive edge-triggered flip-flop

### Register
- N-bit register is a bank of N flip-flops that share a common CLK input
    - all bits of the register are updated at the same time
- key building block of most sequential circuits

### Enabled Flip-Flop
- adds another input called EN or ENABLE to deter- mine whether data is loaded on the clock edge
    - when EN is TRUE, enabled flip-flop behaves like an ordinary D flip-flop
    - when EN is FALSE, enabled flip-flop ignores the clock and retains its state

### Resettable Flip-Flop
- adds another input called RESET
    - when RESET is FALSE, resettable flip-flop behaves like an ordinary D flip-flop
    - when RESET is TRUE, the resettable flip-flop ignores D and resets the output to 0
- useful when need to force a known state (i.e., 0) into all the flip-flops in a system
  when turned on
- synchronously or asynchronously resettable
    - synchronously resettable flip-flops reset themselves only on the rising edge of CLK
    - asynchronously resettable flip-flops reset themselves as soon as RESET becomes TRUE, independent of CLK

### Transistor-Level Latch and Flip-Flop Designs

### Putting it all Together
- latches and flip-flops are fundamental building blocks of sequential circuits
    - D latch is level-sensitive
    - D flip-flop is edge-triggered
    - D latch is transparent when CLK = 1, allowing the input D to flow through to the output Q
    - D flip-flop copies D to Q on the rising edge of CLK
    - at all other times latches and flip-flops retain their old state
    - a register is a bank of several D flip-flops that share a common CLK signal

## 3.3. Synchronous Logic Design
- sequential circuits include all circuits that are not combinational
    - output cannot be determined simply by looking at the current input
- many complex formulations

### Some Problematic Circuits
### Synchronous Sequential Circuits
- cyclic paths - outputs are fed directly back to inputs
    - none in combinational logic
    - cyclic paths can lead to undesirable races or unstable behavior
- break cycles by inserting registers
    - contain state of system at clock edge
    - synchronized to clock
- sequential circuit has a finite set of discrete states {S0, S1,..., Sk−1}
- synchronous sequential circuit has a clock input
    - rising edges indicate a sequence of times at which state transitions occur
- current state and next state distinguish the state of the system at the present from the
  state to which it will enter on the next clock edge
- rules of synchronous sequential circuit composition
    - a circuit is a synchronous sequential circuit if it consists of interconnected circuit elements such that
        - every circuit element is either a register or a combinational circuit
        - at least one circuit element is a register
        - all registers receive the same clock signal
        - every cyclic path contains at least one register
- asynchronous otherwise
- flip-flop is the simplest synchronous sequential circuit
- Two other common types of synchronous sequential circuits
    - finite state machines
    - pipelines

### Synchronous and Asynchronous Circuits
- async more general but sync easier to design and use
    - virtually all digital systems are essentially synchronous
- may need async circuits for communication between systems with different clocks

## 3.4 Finite State Machines
- a circuit with k registers that can be in one of a finite number (2k) of unique states
    - has M inputs, N outputs, and k bits of state
    - receives a clock and, optionally, a reset signal
    - consists of two blocks of combinational logic
        - next state logic
        - output logic
        - a register that stores the state
    - on each clock edge FSM advances to the next state which was computed based on the current
      state and inputs
- two general classes (characterized by functional specifications)
    - Moore machines
        - outputs depend only on the current state of the machine
    - Mealy machines
        - outputs depend on both the current state and the current inputs
- provide a systematic way to design synchronous sequential circuits given a functional
  specification

### FSM Design Example
- start with state transition diagram
    - possible states of system
    - transitions between states
- generate state transition table from transition diagram
    - indicates for each state and input what the next state should be
    - must be assigned binary encodings to build a real circuit
- generate output table
    - indicates what the output should be for each state

### State Encodings
- how to determine the encoding that produces the circuit with the fewest logic gates or the shortest propagation delay?
    - no simple answer
    - must try all possibilities which is infeasible when number of states is large
- binary encoding vs. one-hot encoding
    - one-hot encoding - separate bit of state is used for each state

### Moore and Mealy Machines
- Mealy machines are similar to Moore machines
    - the outputs can depend on inputs as well as the current state
    - in state transition diagrams for Mealy machines the outputs are labeled on the arcs instead
      of in the circles
    - block of combinational logic that computes the outputs uses the current state
      and inputs

### Factoring State Machines
- designing complex FSMs is often easier if they can be broken down into multiple
  interacting simpler state machines such that the output of some machines is the
  input of others

### Deriving an FSM from a Schematic
- examine circuit, stating inputs, outputs, and state bits
- write next state and output equations
- create next state and output tables
- reduce the next state table to eliminate unreachable states
- assign each valid state bit combination a name
- rewrite next state and output tables with state names
- draw state transition diagram
- state in words what the FSM does
    - be careful to succinctly describe the overall purpose and function of the
      FSM
    - do not simply restate each transition of the state transition diagram

### FSM Review
- powerful way to systematically design sequential circuits from a written specification
- use the following procedure to design an FSM:
    - identify the inputs and outputs
    - sketch a state transition diagram
    - for a Moore machine
        - write a state transition table
        - write an output table
    - for a Mealy machine
        - write a combined state transition and output table
    - select state encodings
        - selection affects the hardware design
    - write boolean equations for the next state and output logic
    - sketch the circuit schematic

## 3.5 Timing of Sequential Logic
- flip-flop copies the input D to the output Q on the rising edge of the clock
- sampling D on the clock edge
- if D is stable at either 0 or 1 when the clock rises behavior is clearly defined
- what happens if D is changing at the same time the clock rises?
- sequential element has an aperture time around the clock edge
    - input must be stable for the flip-flop to produce a well-defined output
- aperture of a sequential element is defined
    - setup time - before clock edge
    - hold time - before and after the clock edge
- dynamic discipline limited to using signals that change outside the aperture time
- can think of time in discrete units called clock cycles
- a signal may glitch and oscillate wildly for some bounded amount of time
    - concerned only with its final value at the end of the clock cycle after it has settled to a stable value
    - can simply write A[n], the value of signal A at the end of the nth clock cycle,
      where n is an integer
    - (rather than A(t), the value of A at some instant t, where t is any real number)
- clock period has to be long enough for all signals to settle
    - sets a limit on the speed of the system
    - clock skew - in real systems the clock does not reach all flip-flops at precisely the same time
    - this variation in time increases the necessary clock period
- may be impossible to satisfy the dynamic discipline

### The Dynamic Discipline
- states that the inputs of a synchronous sequential circuit must be stable during the setup
  and hold aperture time around the clock edge
    - guarantees that the flip-flops sample signals while they are not changing
    - can treat signals as discrete in time as well as in logic levels because only concerned with the
      final values of the inputs at the time they are sampled

#### Setup Time Constraint
#### Hold Time Constraint
#### Putting it all Together
- sequential circuits have setup and hold time constraints that dictate the maximum and
  minimum delays of the combinational logic between flip-flops
- modern flip-flops are usually designed so that the minimum delay through the combinational logic is 0
    - flip-flops can be placed back-to-back
- maximum delay constraint limits the number of consecutive gates on the critical path
  of a high-speed circuit
    - a high clock frequency means a short clock period

### Clock Skew
- variation in clock edges (time at which clock reaches registers, etc)
    - wires from the clock source to different registers may be of different lengths
    - noise
    - clock gating
    - use of gated and ungated clocks
- effectively increases both the setup time and the hold time
- adds to the sequencing overhead, reducing the time available for useful work in the combinational
  logic
- increases the required minimum delay through the combinational logic
- designers must not permit too much clock skew
    - sometimes flip-flops are intentionally designed to be particularly slow to prevent hold time problems even
      when the clock skew is substantial

### Metastability
- not always possible to guarantee that the input to a sequential circuit is stable during the
  aperture time especially when the input arrives from the external world

#### Metastable State
- when a flip-flop samples an input that is changing during its aperture the output Q may momentarily take
  on a voltage between 0 and VDD that is in the forbidden zone
  - eventually the flip-flop will resolve the output to a stable state of either 0 or 1
  - the resolution time required to reach the stable state is unbounded
- every bistable device has a metastable state between the two stable states

#### Resolution Time
- if the input to a bistable device such as a flip-flop changes during the aperture time
  the output may take on a metastable value for some time before resolving to a stable 0 or 1
- amount of time required to resolve is unbounded
    - for any finite time, t, the probability that the flip-flop is still metastable is
      nonzero
    - probability drops off exponentially as t increases
- wait long enough can expect with exceedingly high probability that the flip-flop will
  reach a valid logic level

### Synchronizers
- goal of a digital system designer should be to ensure that, given asynchronous inputs, the probability of
  encountering a metastable voltage is sufficiently small
    - to guarantee good logic levels all asynchronous inputs should be passed through synchronizers
- a device that receives an asynchronous input D and a clock CLK
    - produces an output Q within a bounded amount of time
    - output has a valid logic level with extremely high probability
    - if D is stable during the aperture Q should take on the same value as D
    - if D changes during the aperture Q may take on either a HIGH or LOW value but must
      not be metastable

### Derivation of Resolution Time

## 3.6 Parallelism
- speed of a system is characterized by the latency and throughput of information moving through it
- token - group of inputs that are processed to produce a group of outputs
- latency (of a system) - time required for one token to pass through the system from start
  to end
- throughput - number of tokens that can be produced per unit time
- parallelism - imrpovement of throughput by processing several tokens at the same time
- spatial parallelism
    - multiple copies of the hardware are provided so that multiple tasks can be done at the same
     time
- temporal parallelism
    - task is broken into stages like an assembly line
    - multiple tasks can be spread across the stages
    - each task must pass through all stages
    - a different task will be in each stage at any given time so multiple tasks can overlap
- temporal parallelism is commonly called pipelining
- pipelining (temporal parallelism) is particularly attractive because it speeds up a circuit
  without duplicating the hardware
    - registers are placed between blocks of combinational logic to divide the logic into shorter
      stages that can run with a faster clock
    - registers prevent a token in one pipeline stage from catching up with and corrupting the token in the next stage


# Ch. 4 - Hardware Description Languages
- NOTE: see code examples with explanations from repo and book
- 1990s designers became more productive by working at a higher level of abstraction by specifying just the
  logical function and allowing a computer-aided design (CAD) tool to produce the optimized
  gates
    - specifications are generally given in a hardware description language (HDL)
    - two leading hardware description languages are SystemVerilog and VHDL

### Modules
- block of hardware with inputs and outputs
    - behavioral models describe what a module does
    - structural models describe how a module is built from simpler pieces
#### SystemVerilog
```
module sillyfunction(input logic a, b, c, output logic y);
    assign y = ~a & ~b & ~c | a & ~b & ~c | a & ~b & c;
endmodule
```
- begins with the module name and a listing of the inputs and outputs
- assign statement describes combinational logic
- ~ indicates NOT, & indicates AND, and | indicates OR.\
- logic signals such as the inputs and outputs are Boolean variables (0 or 1)
    - (may also have floating and undefined values)
- logic type was introduced in SystemVerilog
    - supersedes the reg type (source of confusion in Verilog)
- logic should be used everywhere except on signals with multiple drivers
- signals with multiple drivers are called nets
#### VHDL
```
library IEEE; use IEEE.STD_LOGIC_1164.all;

entity sillyfunction is
    port(a, b, c: in STD_LOGIC; y: out STD_LOGIC);
end;

architecture synth of sillyfunction is
begin
    y <= (not a and not b and not c) or (a and not b and not c) or (a and not b and c);
end;
```
- three parts
    - library use clause
    - entity declaration
    - architecture body
- entity declaration lists the module name and its inputs and outputs
- architecture body defines what the module does
- signals (inputs and outputs) must have a type declaration
- digital signals should be declared to be STD_LOGIC type
- STD_LOGIC signals can have a value of '0' or '1', as well as floating and undefined values
- STD_LOGIC type is defined in the IEEE.STD_LOGIC_1164 library
- lacks a good default order of operations between AND and OR
    - boolean equations should be parenthesized

### Language Origins
- VHDL is more verbose and cumbersome
- both languages are fully capable of describing any hardware system
- both have quirks
- use is the one that is already being used at site or the one that customers demand
- most CAD tools allow mixture

### Simulation and Synthesis
- logic synthesis transforms HDL code into a netlist describing the hardware
- logic synthesizer might perform optimizations to reduce the amount of hardware required
- netlist may be a text file or drawn as a schematic to help visualize the circuit
- circuit descriptions in HDL resemble code in a programming language
    - remember that the code is intended to represent hardware
    - not all commands can be synthesized into hardware
    - do not think of as a program
    - think of as a description of hardware

## 4.2 Combinational Logic
- synchronous sequential circuits consist of combinational logic and registers
- outputs of combinational logic depend only on the current inputs

### Bitwise Operators
- act on single-bit signals or on multi-bit busses

#### Logic Gates

### Comments and White Space
- not necessary but useful for comprehension
- be consistent with naming conventions

### Reduction Operators
- imply a multiple-input gate acting on a single bus
- eight-input AND gate
    - analogous reduction operators exist for OR, XOR, NAND, NOR, and XNOR gates
- multiple-input XOR performs parity, returning TRUE if an odd number of inputs are TRUE

#### Eight-input AND

### Conditional Assignment
- conditional assignments select the output from among alternatives based on an input called the condition

#### Multiplexer

### Internal Variables
- neither inputs nor outputs but are used only internal to the module
    - similar to local variables in programming language
- assignment statements take place concurrently

#### Full Adder

### Precedence
- precedence tables here

### Numbers
- can be specified in binary, octal, decimal, or hexadecimal
- size, i.e., the number of bits, may optionally be given, and leading zeros are inserted to
  reach this size
- underscores in numbers are ignored and can be helpful in breaking long numbers into more
  readable chunks

### Z's and X's
- use z to indicate a floating value
    - useful for describing a tristate buffer whose output floats when the enable is 0
- use x to indicate an invalid logic level
    - if a bus is simultaneously driven to 0 and 1 by two enabled tristate buffers
      (or other gates), the result is x, indicating contention
- at the start of simulation state nodes such as flip-flop outputs are initialized to an
  unknown state (x in SystemVerilog and u in VHDL)
    - helpful to track errors caused by forgetting to reset a flip-flop before its
      output is used

#### Tristate Buffer
#### Truth Tables with Undefined and Floating Inputs

### Bit Swizzling
- collective of group of operations that operate on a subset of a bus or to concatenate
  signals to form busses

#### Bit Swizzling

### Delays
- HDL statements may be associated with delays specified in arbitrary units
    - helpful during simulation to predict how fast a circuit will work
    - for debugging purposes to understand cause and effect

#### Logic Gates with Delays

## 4.3 Structural Modeling
- describing a module in terms of how it is composed of simpler modules
    - behavioral modeling - describing a module in terms of the relationships between inputs and outputs
- instance - copy of same module
    - distinguished by different names

#### Structural Model fo 4:1 multiplexer
#### Structural Model fo 2:1 multiplexer

- complex systems are designed hierarchically
    - overall system is described structurally by instantiating its major components
    - each component is described structurally from its building blocks recursively until the pieces
      are simple enough to describe behaviorally
    - good style to avoid or minimize mixing structural and behavioral descriptions within a
      single module

#### Accessing Parts of Busses

## 4.4 Sequential Logic
- HDL synthesizers recognize certain idioms and turn them into specific sequential circuits

### Registers
- vast majority of modern commercial systems are built with registers using positive edge-triggered D flip-flops

#### Register

### Resettable Register
- when simulation begins or power is first applied to a circuit the output of a flop or register is unknown
    - indicated with x in SystemVerilog and u in VHDL
- good practice to use resettable registers so that on powerup you can put your system in
  a known state
    - reset may be either asynchronous or synchronous
    - asynchronous reset occurs immediately
    - synchronous reset clears the output only on the next rising edge of the clock

#### Resettable Register

### Enabled Registers
- enabled registers respond to the clock only when the enable is asserted

#### Resettable Enabled Register

### Multiple Registers
- a single always/process statement can be used to describe multiple pieces of hardware

#### Synchronizer

### Latches
- D latch is transparent when the clock is HIGH, allowing data to flow from input to output
    - latch becomes opaque when the clock is LOW, retaining its old state
- not all synthesis tools support latches well
    - use edge-triggered flip-flops instead unless tool does support latches and you have a good
    - ensure HDL does not imply any unintended latches
        - many synthesis tools warn you when a latch is created

## 4.5 More Combinational Logic
#### D Latch
#### Inverter Using always/process

- always/process statements are used to describe sequential circuits because they remember the
  old state when no new state is prescribed
    - always/process statements can also be used to describe combinational logic behaviorally if the
      sensitivity list is written to respond to changes in all of the inputs and the body prescribes
      the output value for every possible input combination
- HDLs support blocking and nonblocking assignments in an always/ process statement
    - group of blocking assignments are evaluated in the order in which they appear in
      the code
    - group of nonblocking assignments are evaluated concurrently
        - all of the statements are evaluated before any of the signals on the left
          hand sides are updated

#### Full Adder Using always/process

### Case Statements

- TODO: more here

#### 3:8 Decoder

### If Statements

#### Priority Circuit

### Truth Tables with Don't Cares

#### Priority Circuit using don't cares

### Blocking and Nonblocking Assignments

#### Blocking and Nonblocking Assignments Guidelines

### Combinational Logic

#### Full Adder Using Nonblocking Assignments

### Sequential Logic

#### Bad Synchronizer with Blocking Assignments

## 4.6 Finite State Machines
- consists of a state register and two blocks of combinational logic to compute the next state and the output given
  the current state and the input
- HDL descriptions of state machines are correspondingly divided into three parts to model
  the state register, the next state logic, and the output logic

#### Divide-by-3 Finite State Machine

#### Pattern Recognizer Moore FSM
#### Pattern Recognizer Mealy FSM

## 4.7 Data Types
- TODO: more here

## 4.8 Parameterized Modules
- TODO: more here

## 4.9 Testbenches
- TODO: more here


# Ch.5 - Digital Building Blocks

## 5.2 Arithmetic Circuits