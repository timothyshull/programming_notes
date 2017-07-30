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