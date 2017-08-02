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
- building blocks of computers
    - addition
    - subtraction
    - comparisons
    - shifts
    - multiplication
    - division

### Half Adder
- 1-bit half adder
    - two inputs, A and B,
    - two outputs, S and Cout
    - S is the sum of A and B
    - if A and B are both 1, S is 2
    - 2 cannot be represented with a single binary digit
        - indicated with a carry out Cout in the next column
    - can be built from an XOR gate and an AND gate.
- multi-bit adder
    - Cout is added or carried in to the next most significant bit
- half adder lacks a Cin input to accept Cout of the previous column
- full adder solves this problem

### Full Adder
- accepts the carry in Cin
```
Cin A B | Cout S
0   0 0 | 0    0
0   0 1 | 0    1
0   1 0 | 0    1
0   1 1 | 1    0
1   0 0 | 0    1
1   0 1 | 1    0
1   1 0 | 1    0
1   1 1 | 1    1
```

### Carry Propagate Adder
- N-bit adder sums two N-bit inputs, A and B, and a carry in Cin to produce an N-bit
  result S and a carry out Cout
    - commonly called a carry propagate adder (CPA) because the carry out of one bit
      propagates into the next bit
    - three common CPA implementations
        - ripple-carry adders
        - carry-lookahead adders
        - prefix adders

### Ripple-Carry Adder
- simplest way to build an N-bit carry propagate adder
    - chain together N full adders
    - Cout of one stage acts as the Cin of the next stage
    - full adder module is reused many times to form a larger system
    - disadvantage of being slow when N is large
    - delay of the adder grows directly with the number of bits

### Carry-Lookahead Adder
- solves problem of ripple-carry adder slowness by dividing the adder into blocks and providing
  circuitry to quickly determine the carry out of a block as soon as the carry in is known
    - looks ahead across the blocks rather than waiting to ripple through all the full adders inside a
      block
- use generate (G) and propagate (P) signals that describe how a column or block determines the carry
  out
    - ith column of an adder is said to generate a carry if it produces a carry out independent
      of the carry in
    - ith column of an adder is guaranteed to generate a carry Ci if Ai and Bi are both 1
    - Gi, the generate signal for column i, is calculated as Gi = AiBi
    - column is said to propagate a carry if it produces a carry out whenever there is a carry
      in

### Prefix adders
- extend the generate and propagate logic of the carry-lookahead adder to perform addition
  faster
    - first compute G and P for pairs of columns
    - then for blocks of 4
    - then for blocks of 8
    - then 16 etc until the generate signal for every column is known
    - sums are computed from these generate signals
- strategy of a prefix adder is to compute the carry in Ci−1 for each column i as quickly as possible
  then compute the sum

### Putting It All Together
- half adder
- full adder
- three types of carry propagate adders
    - ripple-carry
    - carry-lookahead
    - prefix adders
- faster adders require more hardware and therefore are more expensive and power-hungry
- trade-offs must be considered when choosing an appropriate adder for a design
- HDLs provide the + operation to specify a CPA
- modern synthesis tools select among many possible implementations
    - choose the cheapest (smallest) design that meets the speed requirements

#### Adder

### Subtraction
- flip the sign of the second number then add
    - for two’s complement number - invert the bits and add 1

#### Subtractor

### Comparators
- determines whether two binary numbers are equal or if one is greater or less than the other
    - takes as input two N-bit binary numbers A and B
- equality comparator
    - produces a single output indicating whether A is equal to B (A == B)
    - simpler
    - 4-bit equality comparator
        - checks to determine whether the corresponding bits in each column of A and B are equal using XNOR
          gates
        - numbers are equal if all of the columns are equal
- magnitude comparator
    - produces one or more outputs indicating the relative values of A and B
    - usually done by computing A − B and looking at the sign (most significant bit) of the
      result
        - if result is negative (i.e., the sign bit is 1), then A is less than B
        - otherwise A is greater than or equal to B

#### Comparators

### ALU
- combines a variety of mathematical and logical operations into a single unit
    - addition
    - subtraction
    - magnitude comparison
    - AND
    - OR
- forms the heart of most computer system

```
000 A AND B
001 A OR B
010 A+B
011 not used
100 A AND conjugate B
101 A OR conjugate B
110 A−B
111 SLT
```

- set if less than (SLT) operation is used for magnitude comparison
    - when A<B, Y=1
    - otherwise, Y=0
    - (e.g. Y is set to 1 if A is less than B)
- some produce extra outputs (flags) that indicate information about the ALU output
    - an overflow flag indicates that the result of the adder overflowed
    - a zero flag indicates that the ALU output is 0

### Shifters and Rotators
- move bits and multiply or divide by powers of 2
    - shifter shifts a binary number left or right by a specified number of positions
        - logical shifter
            - shifts the number to the left (LSL) or right (LSR) and fills empty spots with 0’s
        - arithmetic shifter
            - same as a logical shifter, but on right shifts fills the most significant bits with a copy of the
              old most significant bit (msb)
            - useful for multiplying and dividing signed numbers
    - rotator rotates number in circle such that empty spots are filled with bits shifted off the other end
- an N-bit shifter can be built from N N:1 multiplexers
- left shift is a special case of multiplication
    - shift by N bits multiplies the number by 2^N
- arithmetic right shift is a special case of division
    - shift by N bits divides the number by 2^N

### Multiplication
- partial products are formed by multiplying a single digit of the multiplier with the entire multiplicand
    - shifted partial products are summed to form the result
- an NxN multiplier multiplies two N-bit numbers and produces a 2N-bit result
    - partial products in binary multiplication are either the multiplicand or all 0’s
- multiplication of 1-bit binary numbers is equivalent to the AND operation
    - AND gates are used to form the partial products

#### Multiplier

### Division
- can be performed using the following algorithm for N-bit unsigned numbers in the range [0, 2N−1]
```
R′=0
for i = N−1 to 0
    R={R′ << 1, Ai}
    D=R−B
    if D < 0 then Qi = 0, R′ = R    // R < B
    else Qi = 1, R′ = D             // R ≥ B
R=R′
```
- partial remainder R is initialized to 0
- most significant bit of the dividend A then becomes the least significant bit of R
- divisor B is repeatedly subtracted from this partial remainder to determine whether it fits
- if difference D is negative (i.e., the sign bit of D is 1), then the quotient bit Qi is 0 and the difference
  is discarded
- otherwise, Qi is 1, and the partial remainder is updated to be the difference
- partial remainder is then doubled (left-shifted by one column)
- the next most significant bit of A becomes the least significant bit of R
- repeat
- result satisfies A/B = Q + R/B

### Further Reading
- Digital Arithmetic - Ercegovac and Lang
- CMOS VLSI Design - Weste and Harris

## 5.3 Number Systems
- fixed-point numbers are analogous to decimals
    - some of the bits represent the integer part
    - remainder represent the fraction
- floating-point numbers are analogous to scientific notation with a mantissa and an exponent

### Fixed-Point Number Systems
- implied binary point between the integer and fraction bits
    - analogous to the decimal point between the integer and fraction digits of an ordinary decimal number
- signed fixed-point numbers can use either two’s complement or sign/magnitude notation
    - in sign/magnitude form the most significant bit is used to indicate the sign
    - in two’s complement is formed by inverting the bits of the absolute value and adding a
      1 to the least significant (rightmost) bit
- just a collection of bits
    - no way of knowing the existence of the binary point except through agreement for interpretation

### Floating-Point Number Systems
- analogous to scientific notation
- circumvent the limitation of having a constant number of integer and fractional bits
    - permits representation of very large and very small numbers
    - sign
    - mantissa (M)
    - base (B)
    - exponent (E)
- 32 bits are used to represent 1 sign bit, 8 exponent bits, and 23 mantissa bits
- binary floating-point
    - first bit of the mantissa (to the left of the binary point) is always 1
        - doesn't need to be stored
        - called the implicit leading one
        - only the fraction bits are stored

#### Special Cases: 0, ±∞, and NaN
- IEEE floating-point standard has special cases
    - zero
    - infinity
    - illegal results
    For example, representing the
- zero is problematic in floating-point notation because of the implicit leading one
- NaN is used for numbers that don't exist

```
Number      Sign        Exponent        Fraction
0           X           00000000        00000000000000000000000
∞           0           11111111        00000000000000000000000
−∞          1           11111111        00000000000000000000000
NaN         X           11111111        Non-zero
```

#### Single- and Double-Precision Formats
- single-precision - 32 bits
- double-precision (doubles) - 64 bits

```
Format  Total Bits  Sign Bits   Exponent Bits   Fraction Bits
single  32          1           8               23
double  64          1           11              52
```

#### Rounding
- arithmetic results that fall outside of the available precision must round to a
  neighboring number
    - round down
    - round up
    - round toward zero
    - round to nearest
    - default rounding mode - round to nearest
- round to nearest mode
    - if two numbers are equally near, the one with a 0 in the least significant position of the fraction is chosen
    - overflows are rounded up to ±∞ and underflows are rounded down to 0
- number overflows when its magnitude is too large to be represented
- number underflows when it is too tiny to be represented

#### Floating-Point Addition
1. extract exponent and fraction bits
2. prepend leading 1 to form the mantissa
3. compare exponents
4. shift smaller mantissa if necessary
5. add mantissas
6. normalize mantissa and adjust exponent if necessary
7. round result
8. assemble exponent and fraction back into floating-point number

## 5.4 Sequential Building Blocks
### Counters
- N-bit binary counter is a sequential arithmetic circuit with clock and reset inputs and an N-bit output Q
    - reset initializes the output to 0
    - counter then advances through all 2N possible outputs in binary order incrementing on the rising edge of the clock
    - can be composed of an adder and a resettable register
    - on each cycle the counter adds 1 to the value stored in the register

#### Counter

### Shift Registers
- shift register
    - clock
    - serial input Sin
    - serial output Sout
    - N parallel outputs QN−1:0
    - on each rising edge of the clock a new bit is shifted in from Sin and all the subsequent contents are shifted forward
    - last bit in the shift register is available at Sout
- can be viewed as serial-to-parallel converters
    - input is provided serially (one bit at a time) at Sin
    - After N cycles the past N inputs are available in parallel at Q.
- can be constructed from N flip-flops connected in series
- can be modified to perform both serial-to-parallel and parallel-to-serial operations by adding a parallel input
  DN−1:0, and a control signal Load

#### Scan Chains
- shift registers are often used to test sequential circuits using a technique called scan chains
- testing combinational circuits is relatively straightforward
- testing sequential circuits is more difficult
    - circuits have state
- starting from a known initial condition
    - large number of cycles of test vectors may be needed to put the circuit into a desired state

#### Shift Register with Parallel Load

## 5.5. Memory Arrays
- digital systems also require memories to store the data used and generated by such circuits
- registers built from flip-flops are a kind of memory that stores small amounts of data
- memory arrays can efficiently store large amounts of data

### Overview
- memory is organized as a two-dimensional array of memory cells
- memory reads or writes the contents of one of the rows of the array
- row is specified by an address
- value read or written is called data
- array with N-bit addresses and M-bit data has 2N rows and M columns
- each row of data is called a word
- array contains 2N M-bit words
- depth of an array is the number of rows
- width is the number of columns (word size)
- size of an array is given as depth × width

#### Bit Cells
 - memory arrays are built as an array of bit cells
    - each stores 1 bit of data
    - each bit cell is connected to a wordline and a bitline
    - memory asserts a single wordline that activates the bit cells in that row for each combination of address bits
    - when the wordline is HIGH, the stored bit transfers to or from the bitline
    - otherwise the bitline is disconnected from the bit cell
- circuitry to store the bit varies with memory type.
- to read a bit cell
    - bitline is initially left floating (Z)
    - wordline is turned ON
        - allows the stored value to drive the bitline to 0 or 1
- to write a bit cell
    - bitline is strongly driven to the desired value
    - wordline is turned ON
        - connectsthe bitline to the stored bit
    - strongly driven bitline overpowers the contents of the bit cell
        - write the desired value into the stored bit

#### Organization
 - practical memories are very large
 - behavior of larger arrays can be extrapolated from smaller arrays
 In this example, the array stores the data from Figure 5.39(b).
 - a memory read
    - a wordline is asserted
    - corresponding row of bit cells drives the bitlines HIGH or LOW
 - a memory write
    - bitlines are driven HIGH or LOW first
    - a wordline is asserted
        - allows the bitline values to be stored in that row of bit cells

#### Memory Ports
- all memories have one or more ports
- each gives read and/or write access to one memory address
- multiported memories can access several addresses simultaneously

#### Memory Types
- memory arrays are specified by their size (depth x width) and the number and type of ports
- all memory arrays store data as an array of bit cells
    - differ in how they store bits
- classified based on how they store bits in the bit cell
- broadest classification
    - random access memory (RAM) vs. read only memory (ROM)
- RAM
    - volatile - loses its data when the power is turned off
- ROM
    - nonvolatile - retains its data indefinitely, even without a power source
- RAM is called random access memory because any data word is accessed with the same delay as any other
- sequential access memory
    - tape recorder
        - accesses nearby data more quickly than faraway data
- ROM is called read only memory because it could only be read but not written
- ROMs are randomly accessed too
- most modern ROMs can be written as well as read
- important distinction to remember is that RAMs are volatile and ROMs are nonvolatile
- two major types of RAMs
    - dynamic RAM (DRAM)
        - stores data as a charge on a capacitor
    - static RAM (SRAM)
        - stores data using a pair of cross-coupled inverters
- many flavors of ROMs that vary by how they are written and erased

### Dynamic Random Access Memory (DRAM)
- stores a bit as the presence or absence of charge on a capacitor
    - nMOS transistor behaves as a switch that either connects or disconnects the capacitor from the bitline
    - when wordline is asserted
        - nMOS transistor turns ON and the stored bit value transfers to or from the bitline.
    - when the capacitor is charged to VDD the stored bit is 1
    - when it is discharged to GND the stored bit is 0
    - capacitor node is dynamic because it is not actively driven HIGH or LOW by a transistor tied to VDD or GND
- on read
    - data values are transferred from the capacitor to the bitline
    - destroys the bit value stored on the capacitor
        - data word must be restored (rewritten) after each read
- on write
    - data values are transferred from the bitline to the capacitor
- even when DRAM is not read the contents must be refreshed (read and rewritten) every few milliseconds
  because the charge on the capacitor gradually leaks away

### StaticRandomAccessMemory(SRAM)
- static because stored bits do not need to be refreshed
    - data bit is stored on cross-coupled inverters
    - each cell has two outputs - bitline and bitline
        - when the wordline is asserted both nMOS transistors turn on and data values are transferred to or from the
          bitlines
    - if noise degrades the value of the stored bit the cross-coupled inverters restore the value (unlike DRAM)

### Area and Delay
- flip-flops, SRAMs, and DRAMs are all volatile memories
    - each has different area and delay characteristics
```
Memory Type     Transistors per Bit Cell    Latency
flip-flop       ~20                         fast
SRAM            6                           medium
DRAM            1                           slow
```
- data bit stored in a flip-flop is available immediately at its output
    - take at least 20 transistors to build
    - more transistors a device has the more area, power, and cost it requires
- DRAM latency is longer than that of SRAM because its bitline is not actively driven by a transistor
    - must wait for charge to move (relatively) slowly from the capacitor to the bitline
    - also fundamentally has lower throughput than SRAM because it must refresh data periodically and after
      a read
    - technologies such as synchronous DRAM (SDRAM) and double data rate (DDR) SDRAM have been developed to overcome
      this problem
    - SDRAM uses a clock to pipeline memory accesses
    - DDR SDRAM, sometimes called simply DDR, uses both the rising and falling edges of the clock to access data
      doubling the throughput for a given clock speed
    - DDR was first standardized in 2000 and ran at 100 to 200 MHz
    - DDR2, DDR3, and DDR4, increased the clock speeds, with speeds in 2012 being over 1 GHz
- latency and throughput also depend on memory size
    - larger tend to be slower than smaller
- best memory type for a particular design depends on the speed, cost, and power constraints

### Register Files
- register file - group of registers
    - usually built as a small, multiported SRAM array
        - more compact than an array of flip-flops

### Read Only Memory
- stores a bit as the presence or absence of a transistor
- to read the cell
    - bitline is weakly pulled HIGH
    - wordline is turned ON
    - if the transistor is present, it pulls the bitline LOW
    - if it is absent, the bitline remains HIGH
    - ROM bit cell is a combinational circuit and has no state to "forget" if power is turned off
- conceptually ROMs can be built using two-level logic with a group of AND gates followed by a group of OR gates
    - AND gates produce all possible minterms and hence form a decoder
    - ROM can perform any two-level logic function
- in practice ROMs are built from transistors instead of logic gates to reduce their size and cost
- contents of the ROM bit cell during manufacturing by the presence or absence of a transistor in each bit cell
- programmable ROM (PROM) places a transistor in every bit cell but provides a way to connect or disconnect the
  transistor to ground
- fuse-programmable ROM
    - user programs the ROM by applying a high voltage to selectively blow fuses
    - if the fuse is present the transistor is connected to GND and the cell holds a 0
    - if the fuse is destroyed the transistor is disconnected from ground and the cell holds a 1
    - also called a one-time programmable ROM because the fuse cannot be repaired once it is blown
- reprogrammable ROMs provide a reversible mechanism for connecting or disconnecting the transistor to GND
- erasable PROMs (EPROMs) replace the nMOS transistor and fuse with a floating-gate transistor
    - floating gate is not physically attached to any other wires
    - when suitable high voltages are applied electrons tunnel through an insulator onto the floating gate,
      turning on the transistor and connecting the bitline to the wordline (decoder output)
    - when the EPROM is exposed to intense ultraviolet (UV) light for about half an hour
      the electrons are knocked off the floating gate turning the transistor off
    - programming and erasing
- electrically erasable PROMs (EEPROMs) and flash memory use similar principles but include circuitry on the
  chip for erasing as well as programming
    - EEPROM bit cells are individually erasable
    - flash memory erases larger blocks of bits and is cheaper because fewer erasing circuits are needed
- flash has become an extremely popular way to store large amounts of data in portable battery-powered systems such as
  cameras and music players
- modern ROMs are not really read only
    - can be programmed (written) as well
- difference between RAM and ROM is that ROMs take a longer time to write but are nonvolatile

### Logic Using Memory Arrays
- used primarily for data storage
- can also perform combinational logic functions
- a 2N-word x M-bit memory can perform any combinational function of N inputs and M outputs
- memory arrays used to perform logic are called lookup tables (LUTs)
- by using memory to perform logic the user can look up the output value for a given input combination (address)
    - each address corresponds to a row in the truth table
    - each data bit corresponds to an output value

### Memory HDL

#### RAM

#### ROM

## 5.6 Logic Arrays
- gates can be organized into regular arrays
- if the connections are made programmable these logic arrays can be configured to perform any function without the
  user having to connect wires in specific ways
- logic arrays are mass produced in large quantities
    - inexpensive
- software tools allow users to map logic designs onto these arrays
- most are also reconfigurable
    - valuable during development and also useful in the field
    - system can be upgraded by simply downloading the new configuration

### Programmable Logic Array
- implement two-level combinational logic in sum-of-products (SOP) form
- built from an AND array followed by an OR array
- inputs (in true and complementary form) drive an AND array
    - produces implicants which in turn are ORed together to form the outputs
- ROMs can be viewed as a special case of PLAs
    - decoder behaves as an AND plane that produces all 2^M minterms
    - ROM array behaves as an OR plane that produces the outputs
    - if the function does not depend on all 2^M minterms a PLA is likely to be smaller than a ROM
- simple programmable logic devices (SPLDs) are enhanced PLAs that add registers and various
  other features to the basic AND/OR planes
    - SPLDs and PLAs have largely been displaced by FPGAs which are more flexible and efficient for
      building large systems

### Field Programmable Gate Array
- an array of reconfigurable gates
- user can implement designs on the FPGA using either an HDL or a schematic
- more powerful and more flexible than PLAs
    - can implement both combinational and sequential logic
    - can implement multi-level logic functions (PLAs can only implement two-level logic)
    - built-in multipliers
    - high-speed I/Os
    - data converters including analog-to-digital converters
    - large RAM arrays
    - processors
- built as an array of configurable logic elements (LEs)
    - referred to as configurable logic blocks (CLBs)
    - each LE can be configured to perform combinational or sequential functions
- LEs are surrounded by input/output elements (IOEs) for interfacing with the outside world
    - IOEs connect LE inputs and outputs to pins on the chip package
    - LEs can connect to other LEs and IOEs through programmable routing channels
- Altera Corp.
- Xilinx, Inc.
- designer configures an FPGA by first creating a schematic or HDL description of the design
    - design is synthesized onto the FPGA
    - synthesis tool determines how the LUTs, multiplexers, and routing channels should be configured to perform the
      specified functions
    - configuration information is downloaded to the FPGA
    - FPGA may download its SRAM contents from a computer in the laboratory or from an EEPROM chip when the system is
      turned on
    - some manufacturers include an EEPROM directly on the FPGA or use one-time programmable fuses to configure the FPGA

### Array Implementations
- ROMs and PLAs commonly use pseudo-nMOS or dynamic circuits instead of conventional logic gates to minimize their
  size and cost
- PLAs can be built using pseudo-nMOS circuits


# Ch. 6 - Architecture
## 6.1 Introduction
- architecture is the programmer’s view of a computer
    - defined by the instruction set (language) and operand locations (registers and memory)
- different architectures exist
    - x86
    - MIPS
    - SPARC
    - PowerPC
- to understanding any computer architecture learn its language
    - instructions = words
    - instruction set = vocabulary
    - instructions indicate both the operation to perform and the operands to use
    - operands may come from memory, from registers, or from the instruction itself
    - instructions are encoded as binary numbers in a format called machine language
- microprocessors are digital systems that read and execute machine language instructions
    - represent the instructions in a symbolic format called assembly language
- instruction sets of different architectures like different dialects than different languages
    - almost all architectures define basic instructions that operate on memory or registers
- architecture does not define the underlying hardware implementation
    - many different hardware implementations of a single architecture exist
    - Intel and Advanced Micro Devices (AMD) both sell various microprocessors belonging to the
      same x86 architecture
- microarchitecture - the specific arrangement of registers, memories, ALUs, and other building blocks to form a microprocessor
    - many different microarchitectures exist for a single architecture
- use MIPS architecture
    - first developed by John Hennessy and colleagues at Stanford in the 1980s
    - used by
        - Silicon Graphics
        - Nintendo
        - Cisco

## 6.2 Assembly Language
- human-readable representation of the computer’s native language
- instruction specifies operation to perform and the operands on which to operate

### Instructions
- mnemonic - indicates what operation to perform
- source operands - operands (data sources) on which operation is performed
- destination operand - operand (data location) to which the result of the operation is
  written
- MIPS instruction set
    - number of instructions is kept small so that the hardware required to decode the instruction and its operands
      can be simple, small, and fast
    - elaborate operations (less common) are performed using sequences of multiple simple instructions
- MIPS is a reduced instruction set computer (RISC) architecture
- complex instruction set computers (CISC)
    - architectures with many complex instructions
    - Intel x86 architecture
- the cost of implementing complex instructions in a CISC architecture is added hardware and overhead that slows
  down simple instructions
- RISC architecture minimizes the hardware complexity and the necessary instruction encoding by keeping the set of
  distinct instructions small

### Operands: Registers, Memory, and Constants
- instruction operates on operands
- computers operate on 1’s and 0’s - not variable names
- instructions need a physical location from which to retrieve the binary data
- operands can be stored in registers or memory, or they may be constants stored in the instruction itself
- computers use various locations to hold operands in order to optimize for speed and data capacity
- operands stored as constants or in registers are accessed quickly but they hold only a small amount of data
    - additional data must be accessed from memory (large but slow)
- MIPS - 32-bit architecture
    - extended to 64 bits in commercial products (which may be standard now)

#### Registers
- instructions need to access operands quickly so that they can run fast
- operands stored in memory take a long time to retrieve
- most architectures specify a small number of registers that hold commonly used operands
- MIPS architecture uses 32 registers
    - register set or register file
    - fewer registers = faster access
- small register file is typically built from a small SRAM array
    - uses a small decoder and bitlines connected to relatively few memory cells
    - has a shorter critical path than a large memory does
- MIPS generally stores variables in 18 of the 32 registers
    - $s0–$s7
    - $t0–$t9
- register names beginning with $s are called saved registers
    - store standard local variables
    - special purpose when they are used with function calls
- register names beginning with $t are called temporary registers
    - used for storing temporary variables

#### The Register Set
```
Name        Number      Use
$0          0           the constant value 0
$at         1           assembler temporary
$v0-$v1     2-3         function return value
$a0-$a3     4-7         function arguments
$t0-$t7     8-15        temporary variables
$s0-$s7     16-23       saved variables
$t8-$t9     24-25       temporary variables
$k0-$k1     26-27       operating system temporaries
$gp         38          global pointer
$sp         29          stack pointer
$fp         30          frame pointer
$ra         31          function return address
```

#### Memory
- data can also be stored in memory
- memory has many data locations but accessing it takes a longer
- register file = small and fast
- memory = large and slow
- commonly used variables are kept in registers
- MIPS architecture uses 32-bit memory addresses and 32-bit data words (may be outdated)
    - byte-addressable memory - each byte in memory has a unique address
- by convention memory is drawn with low memory addresses toward the bottom and high memory addresses toward the top
- MIPS uses the load word instruction (lw) to read a data word from memory into a register
- lw instruction specifies the effective address in memory as the sum of a base address and an offset
    - base address is a register
        - written in parentheses in the instruction
    - offset is a constant
        - written before the parentheses
- store word instruction (sw) to write a data word from a register into memory
- any register can be used to supply the base address
- MIPS memory model is byte-addressable, not word-addressable
    - each byte has a unique address
    - 32-bit word consists of four 8-bit bytes so each word address is a multiple of 4
    - both the 32-bit word address and the data value are given in hexadecimal
- also provides the lb and sb instructions that load and store single bytes in memory rather than words
    - similar to lw and sw
- byte-addressable memories are organized big-endian or little-endian
    - both - most significant byte (MSB) is on the left and the least significant byte (LSB) is on the right
    - big-endian machines - bytes are numbered starting with 0 at the big (most significant) end
    - little-endian machines - bytes are numbered starting with 0 at the little (least significant) end
    - word addresses are the same in both formats and refer to the same four bytes
        - only the addresses of bytes within a word differ
- IBM PowerPC (old Macs) uses big-endian
- Intel x86 architecture (found in PCs and new Macs) uses little-endian
- some MIPS processors are little-endian and some are big-endian
- choice of endianness is completely arbitrary
    - causes problems when sharing data between computers
- word addresses for lw and sw must be word aligned for MIPS
    - the address must be divisible by 4

#### Constants/Immediates
- constants/immediates - values are immediately available from the instruction and do not require a register or memory
  access
  Add immediate,
- addi - add immediate
    - adds the immediate specified in the instruction to a value in a register
- immediate specified in an instruction is a 16-bit two’s complement number in the range [–32,768, 32,767]
- subtraction is equivalent to adding a negative number
    - no subi instruction in the MIPS architecture
- MIPS instruction set makes the compromise of supporting three instruction formats
    - three register operands
    - two register operands and a 16-bit immediate
    - 26-bit immediate

## 6.3 Machine Language
- program written in assembly language is translated from mnemonics to a representation using only 1’s and 0’s
- MIPS uses 32-bit instructions
    - encodes all instructions as words that can be stored in memory.
    - some instructions may not require all 32 bits of encoding
    - variable-length instructions would add too much complexity
- MIPS makes the compromise of defining three instruction formats
    - R-type - three registers
    - I-type - two register operands and a 16-bit immediate
    - J-type - (jump instructions) 26-bit immediate

### R-Type Instructions
- short for register-type
- three registers as operands
    - two as sources
    - and one as a destination
```
op      rs      rt      shamt   funct
6 bits  5 bits  5 bits  5 bits  6 bits
```
- 32-bit instruction has six fields
    - op (also called opcode or operation code)
        - all R-type instructions have an opcode of 0
    - rs - source operand
    - rt - source operand
    - rd - destination operand
    - shamt
        - used only in shift operations
        - binary value stored indicates the amount to shift
        - 0 for all other R-type instructions
    - funct (also called the function)
        - specifies the operation to be performed
    - operation performed encoded in op and funct

###  l-Type Instructions
- short for immediate-type
- two register operands and one immediate operand
```
op      rs      rt      imm
6 bits  5 bits  5 bits  16 bits
```
- 32-bit instruction has four fields
    - op
        - specifies opcode which determines operation
    - rs
        - always source operand
    - rt
        - source operand for most
        - destination operand for some
        - listed first in assembly when used as destination
        - always second register field in machine language instruction
    - imm
        - holds the 16-bit immediate operand
        - negative immediate values are represented using 16-bit two’s complement notation
        - 16-bit immediate field but used in 32-bit operations
            - upper half should be all 0’s for positive immediates
            - upper half should be all 1’s for negative immediates
            - called sign extension
                - N-bit two’s complement number is sign-extended to an M-bit number (M > N) by copying the sign bit
                  (most significant bit) of the N-bit number into all of the upper bits of the M-bit number
                - does not change its value
- most MIPS instructions sign-extend the immediate
    - an exception to this rule is that logical operations (andi, ori, xori) place 0’s in the upper half
      (called zero extension)

### J-Type Instructions
- short for jump-type
- used only with jump instructions
```
op          addr
6 bits      26 bits
```
- op
    - 6-bit opcode for operation to perform (some jump)
- addr
    - single 26-bit address operand to specify an address

### Interpreting Machine Language Code
- decipher the fields of each 32-bit instruction word
- all formats start with a 6-bit opcode field
    - start with opcode
        - if 0 - R-type
        - otherwise I-type or J-type

### The Power of the Stored Program
- machine language
    - series of 32-bit numbers representing instructions
    - instructions can be stored in memor
- called the stored program concept
    - key reason why computers are so powerful
    - running a different program does not require large amounts of time and effort to reconfigure or rewire hardware
    - only requires writing the new program to memory
    - general purpose computing
- instructions in a stored program are retrieved, or fetched, from memory and executed by the processor
- MIPS programs
    - instructions are normally stored starting at address 0x00400000
    - instruction addresses advance by 4 bytes, not 1
    - processor fetches the instructions from memory sequentially to run or execute the stored program
    - fetched instructions decoded and executed by hardware
    - address of current instruction stored in 32-bit register called the program counter (PC)
        - separate from the 32 registers
        - operating system sets the PC to address 0x00400000
        - processor reads the instruction at that memory address and executes the instruction
        - processor increments PC by 4 to 0x00400004, fetches and executes that instruction
        - repeat
- architectural state of microprocessor holds state of a program
- for MIPS the architectural state consists of the register file and PC
    - if OS saves state at some point in the program it can interrupt the program, do something else
      then restore the state and continue program properly

## 6.4 Programming
### Arithmetic/Logical Instructions
#### Logical Instructions
- include and, or, xor, and nor (R-type)
- operate bit-by-bit on two source registers and write the result to the destination register
 The figure shows the values stored in the destination register
 , rd, after the instruc- tion executes.
- and instruction is useful for masking bits (i.e., forcing unwanted bits to 0)
    - any subset of register bits can be masked
- or instruction is useful for combining bits from two registers
- MIPS does not provide a NOT instruction
    - NOR instruction can substitute
        - A NOR $0 = NOT A
- logical operations can also operate on immediates
    - I-type instructions are andi, ori, and xori
    - nori is not provided
        - easily implemented using other instructions

#### Shift Instructions
- shift the value in a register left or right by up to 31 bits
    - multiply or divide by powers of two
- sll (shift left logical), srl (shift right logical), and sra (shift right arithmetic)
- left shifts always fill the least significant bits with 0’s
- right shifts can be either logical (0’s shift into the most significant bits) or arithmetic (the sign bit shifts
  into the most significant bits)
- shifting a value left by N is equivalent to multiplying it by 2^N
- arithmetically shifting a value right by N is equivalent to dividing it by 2^N
- MIPS also has variable-shift instructions
    - sllv (shift left logical vari- able), srlv (shift right logical variable), and srav (shift right arithmetic variable)
    - of the form sllv rd, rt, rs
        - order of rt and rs is reversed from most R-type instructions
     - rt ($s1) holds the value to be shifted
     - five least significant bits of rs ($s2) give the amount to shift
     - shifted result is placed in rd
     - shamt field is ignored and should be all 0’s


- TODO: more here


# Ch. 7 - Microarchiecture
## 7.1 Introduction
- connection between logic and architecture
    - specific arrangement of registers, ALUs, finite state machines (FSMs), memories, and other logic building
      blocks needed to implement an architecture
- particular architecture (MIPS) may have many different microarchitectures
    - different trade-offs of performance, cost, and complexity
    - all run the same programs
    - internal designs vary widely

### Architectural State and Instruction Set
- computer architecture is defined by its instruction set and architectural state
- architectural state for MIPS consists
    - program counter
    - 32 registers
- any MIPS microarchitecture must contain all of this state
- based on the current architectural state the processor executes a particular instruction with a particular set of
  data to produce a new architectural state
- some microarchitectures contain additional nonarchitectural state to either simplify the logic or improve performance
- consider only a subset of the MIPS instruction set for simplicity
    - R-type arithmetic/logic instructions
        - add, sub, and, or, slt
    - memory instructions
        - lw, sw
    - branches
        - beq
    - extended to handle addi and j
    - sufficient to write many interesting programs

### Design Process
- two interacting parts
    - datapath
        - operates on words of data
        - contains structures such as memories, registers, ALUs, and multiplexers
    - control unit
        - receives the current instruction from the datapath and tells the datapath how to execute instruction
        - produces multiplexer select, register enable, and memory write signals to control the operation of the
          datapath
- start with hardware containing the state elements
    - include the memories and the architectural state (the program counter and registers)
- add blocks of combinational logic between the state elements to compute the new state based on the current state
- instruction is read from part of memory
    - load and store instructions then read or write data from another part of memory
- partition the overall memory into two smaller memories
    - instructions
    - data
- four state elements
    - the program counter
    - register file
    - instruction memory
    - data memory
- data busses
- address busses
- control signals
- program counter
    - ordinary 32-bit register
    - output, PC, points to the current instruction
    - input, PC', indicates the address of the next instruction
- instruction memory
    - single read port
    - takes a 32-bit instruction address input, A
    - reads the 32-bit data (i.e., instruction) from that address onto the read data output, RD
- 32-element x 32-bit register file
    - two read ports and one write port
    - read ports take 5-bit address inputs, A1 and A2, each specifying one of 25 = 32 registers as source operands
    - read the 32-bit register values onto read data outputs RD1 and RD2, respectively
    - write port takes
        - 5-bit address input, A3
        - 32-bit write data input, WD
        - write enable input, WE3
        - a clock
    - if write enable is 1 the register file writes the data into the specified register on the rising edge of the
      clock
- data memory
    - single read/write port
    - if the write enable, WE, is 1 it writes data WD into address A on the rising edge of the clock
    - if the write enable is 0, it reads address A onto RD
- instruction memory, register file, and data memory are all read combinationally
    - if the address changes the new data appears at RD after some propagation delay
    - no clock is involved
    - written only on the rising edge of the clock
- state of the system is changed only at the clock edge
- address, data, and write enable must setup sometime before the clock edge and must remain stable until a
  hold time after the clock edge
- state elements are synchronous sequential circuits because they change their state only on the rising edge of the clock
- microprocessor is built of clocked state elements and combinational logic
    - also synchronous sequential circuit
- processor can be viewed as a
    - giant finite state machine
    - a collection of simpler interacting state machines

### MIPS Microarchitectures
- three microarchitectures viewed for the MIPS processor architecture
    - differ in the way that the state elements are connected together and in the amount of nonarchitectural state
    - single-cycle
        - executes an entire instruction in one cycle
        - does not require any non-architectural state
        - cycle time is limited by the slowest instruction
    - multicycle
        - executes instructions in a series of shorter cycles
        - simpler instructions execute in fewer cycles than complicated ones
        - reduces the hardware cost by reusing expensive hardware blocks such as adders and memories
            - adder may be used on several different cycles for several purposes while carrying out a single instruction
            - accomplishes this by adding several nonarchitectural registers to hold intermediate results
        - executes only one instruction at a time
        - each instruction takes multiple clock cycles
    - pipelined
        - applies pipelining to the single-cycle microarchitecture
        - can execute several instructions simultaneously
        - improves the throughput significantly
        - must add logic to handle dependencies between simultaneously executing instructions
        - requires nonarchitectural pipeline registers
        - added logic and registers are worthwhile
            - all commercial high-performance processors use pipelining today

## 7.2 Performance Analysis
- only gimmick-free way to measure performance is by measuring the execution time of a program of interest to
  application domain in question
    - the computer that executes the program fastest has the highest performance
- next best choice is to measure the total execution time of a collection of programs that are similar
- called benchmarks
    - execution times of these programs are commonly published
- execution time of a program, measured in seconds, is given by
```
Execution Time = (# instructions)(cycles/instruction)(seconds/cycle)
```
- number of instructions in a program depends on the processor architecture
    - some architectures have complicated instructions that do more work per instruction
        - complicated instructions are often slower to execute in hardware
    - number of instructions also depends on the programmer
- number of cycles per instruction, often called CPI, is the number of clock cycles required to execute an average
  instruction
    - reciprocal of the throughput (instructions per cycle, or IPC)
    - different microarchitectures have different CPIs
- number of seconds per cycle is the clock period, Tc
    - determined by the critical path through the logic on the processor
    - different microarchitectures have different clock periods
    - logic and circuit designs also significantly affect the clock period
- challenge of the microarchitect is to choose the design that minimizes the execution time while satisfying
  constraints cost and/or power consumption
- determining the best choice requires careful analysis because microarchitectural decisions affect both CPI and Tc
  and are influenced by logic and circuit designs
- many other factors that affect overall computer performance
    - hard disk
    - memory
    - graphics system
    - network connection

## 7.3 Single-Cycle Processor
- first design
    - MIPS microarchitecture that executes instructions in a single cycle
- start by constructing the datapath by connecting state elements with combinational logic that can execute the various
  instructions
- control signals determine which specific instruction is carried out by the datapath at any given time
- controller contains combinational logic that generates the appropriate control signals based on the current
  instruction

### Single-Cycle Datapath
- development of a single-cycle datapath adding one piece at a time to the state elements
- program counter (PC) register contains the address of the instruction to execute
    - read this instruction from instruction memory
    - PC is simply connected to the address input of the instruction memory
    - instruction memory reads out, or fetches, the 32-bit instruction, labeled Instr
- processor’s actions depend on the specific instruction that was fetched
- first determine datapath connections for the lw instruction then generalize the datapath to handle the other
  instructions
- lw instruction
    - read the source register containing the base address
        - specified in the rs field of the instruction, Instr25:21
        - these bits of the instruction are connected to the address input of one of the register file read ports, A1
    - register file reads the register value onto RD1
    - requires an offset
        - stored in the immediate field of the instruction, Instr15:0
        - 16-bit immediate might be either positive or negative, it must be sign-extended to 32 bits
        - 32-bit sign-extended value is called SignImm
        - (sign extension simply copies the sign bit (most significant bit) of a short input into all of the upper bits
           of the longer output)
        - SignImm15:0 = Instr15:0 and SignImm31:16 = Instr15
    - processor must add the base address to the offset to find the address to read from memory
    - requires an ALU to perform this addition
        - receives two operands, SrcA and SrcB
        - SrcA comes from the register file, and SrcB comes from the sign-extended immediate
        - 3-bit ALUControl signal specifies the operation
        - generates a 32-bit ALUResult and a Zero flag, that indicates whether ALUResult == 0
        - ALUControl signal should be set to 010 to add the base address and offset for lw
        - ALUResult is sent to the data memory as the address for the load instruction
        - data is read from the data memory onto the ReadData bus, then written back to the destination register in the
          register file at the end of the cycle
        - port 3 of the register file is the write port