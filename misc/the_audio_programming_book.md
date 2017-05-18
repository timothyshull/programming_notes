# Ch. 2 - Audio Programming in C
## 2.1 - A Simple Soundfile Library: portsf
- simple sound file access
- not designed as an alternative to heavy-weight libsndfile

### The Makefile

### Soundfile Formats - enum and typedef
- used to define a list of symbols and (in C by default and C++ without a scoped enum)
  to associate them with ascending integer values
- using the typedef keyword (in C) effectively defines a new type and instead
  associates the symbol names with this type (even though they still effectively
  decay to ints)
- portsf uses enums to define soundfile channel properties, file formats, sample
  type
    - psf_channelformat
    - psf_format
    - psf_stype
- uses a struct + typedef to encapsulate soundfile properties
    - PSF_PROPS
        - long srate
        - long chans
        - psf_stype samptype
        - psf_format format
        - psf_channelformat chformat
- interleaved samples
```
 chan 1  chan 2  chan 1  chan2
-------------------------------
|  01   |  02   |  03   |  04  | ...
-------------------------------
     frame 01       frame 02
```
- sample rate - number of samples per second (22050, 44100, 48000, 96000, 192000)
- channels - number of independent I/O sources represented in an audio file
  (mono, stereo, six for Dolby). Sample rate defines the frame rate where one frame
  contains one sample for each channel.
- sample type - the data format of each sample
    - common - 16-bit integer, 24-bit "packed" integer, 32-bit floating point values
    - less common - 32-bit integer, 8-bit integer
    - recent - 64-bit floating point
- format - file format for sound file type
    - wave, AIFF, etc
- channel format - means for associating audio channels with specific speaker positions

### Initializing the portsf Library
- requires calling psf_init and psf_finish to init and clean up library

### Opening a Soundfile for Reading
```
int psf_sndOpen(const char *path, pSF_pROpS *props, int rescale);
```
- call psf_sndOpen
    - pass path
    - pointer to PSF_PROPS struct that gets populated with soundfile info
- wave and AIFF files can contain position and value of the peak sample in the file
  in a header chunk
- third arg can use this value to rescale the samples in the file to fit samples to a
  range

### Opening a Soundfile for Writing
```
int psf_sndCreate(const char *path, const PSF-PROPS *props, int clip_floats, int ninheader, int mode);
```
- clip_floats will clip floating point value samples to the range -1.0 to +1.0
- minheader will create wave files with a minimum header and no peak data
- mode sets read/write permissions for the file (uses psf_create_mode enum)

### Setting a File Format from the Name
- simplest for command line programs (and arguably GUI as well) to allow user
  to indicate the soundfile format simply by entering the name and interpreting
  format from the extension

### Closing Soundfiles (and Recording Peak Sample Value with the PEAK Chunk)
```
int psf_sndclose(int sfd) ;
```
- pass soundfile descriptor returned from the open call
- will update peak data if specified by tracking maximum file per channel when the
  file is closed (must set minheader to 0)
- can use
```
long psf_sndReadPeaks(int sfd, PSF_CHPEAK peakdata[ ], long *peaktime) ;
```

### Reading and Writing - The Sample Frame
- portsf automatically performs all the low-level calculations and conversions
  required to read sample frames of any supported type into the user sample buffer
```
long psf_sndReadFloatFrames(int sfd, float *buf, DWORD nFrames)i
long psf_sndWriteFloatFrames(int sfd, const float *buf, DWoRD nFrames);
```
- user responsibility to supply buffer of at least nFrames * Props.chans * sizeof(float) bytes
- can copy with the following simple function
```
float* frame = (float*) malloc(props.chans * sizeof(float));
/* copy file one (multi-channel) frame at a ti-me */
framesread = psf_sndReadFloatFrames(ifd, frame, L ) ;
while (framesread == 1) {
    psf_sndWriteFloatFrames(ofd, frame, 1);
    framesread = psf_sndReadFloatFrames (ifd, frame, 1);
}
```

### Streamlining Error Handling - the `goto` Keyword
- easy but bad way to DRY out C error handling code

### Using portsf for Soundfile Conversion with PEAK Support
- code here

### Building Programs with portsf

## 2.2 - Processing Audio
### The First Audio Process: Changing Level
- basic structure
    1. define variables
    2. obtain and validate arguments from user
    3. allocate memory, open infile and outfile
    4. perform main processing loop
    5. report to the user
    6. close files, free memory
- processing code
```
while (framesread == 1) {
    totalread++;
    for(i = 0; i < props.chanst i++) {
        frame[i] *= ampfac;
    }
    if(psf_sndwriteFloatFrames(ofd, frame, 1) != 1) {
        printf("Error writing to outfile\n");
        error++;
        break;
    }
    framesread = psf_sndReadFloatFrames(ifd, frame, 1);
}
```

### Amplitude vs. Loudness - the Decibel
- amplitude to decibels
```
amplitude_in_db = 20 * log10(amp)
```
- amp is in range 0 to 1.0
- db to amplitude
```
amp = 10 * (loudness_in_db / 20)
```
- logarithmic decibel more closely mirrors human hearing
- decibel value is a ratio comparison between two values
- need a pre-defined reference level for decibel comparisons
- bit size of samples dictates dynamic range of system

### Extending sfgain - Normalization
- normalization - set sound or track to a relative level
- best practice to not normalize to digital peak but to leave at least 3db headroom
- sfgain program can be seen as a template for a normalization tool
- basic structure
    1. set up variables
    2. obtain and validate arguments from user
    3. allocate memory, open the infile
    4. read peak amplitude of ihe infile; if not found
    5. scan the whole infile to obtain peak value; rewind file ready for processing stage. stage 4c: if peak > 0, open outfiie; otherwise, quit
    6. perform main processing loop
    7. report to the user
    8. close files, free memory
- obtain max sample
```
double maxsamp(float* buf, unsigned long blocksize)
{
    double absval, peak = 0.0; unsigned long i;
    for (i = 0; i < blocksize; i++) {
        absval = fabs(buf [i]);
        if(absval > peak) {
            peak = absval;
        }
    }
    return peak;
}

// processing
framesread = psf_sndReadFloatFrames(ifd, frame, 1);
while (framesread == f) {
    double thispeak;
    blocksize = props.chans;
    thispeak = maxsamp(frame, blocksize);
    if (thispeak > inpeak) {
        inpeak = thispeak;
    }
    framesread = psf sndReadFloatFrames(ifd. frame, 1);
}
```
- better to use a buffer size and process many samples on each loop pass
- calculating the scale factor
```
scalefac = (float) (ampfac / inpeak);
```

## 2.3 - Stereo Panning
### Introduction
### Refinements to the Template Code
- basic structure
    1. allocate memory, open infile.
    4. perform any special data pre-processing, opening of extra data files, etc. stage 4b: open outfile, once all required resources are obtained
    5. perform main processing loop
    6. report to the user
    7. close files, free memory

### A Simple Formula for Stereo Panning
- use struct for left and right pan position values
```
PANPOS simplepan(double position)
{
    PANPOS pos;
    position *= 0.5;
    pos.left = position - 0.5;
    pos.right = position + 0.5;
    return posi;
}
```

### Multiple Conditional Tests - The Logical || and && Operators
- check that pan position is not < -1.0 and not > 1.0

### Changing Format from Input to Output - Mono to Stereo
- need to handle output buffer that is twice as large as input buffer

### A Processing Loop to Do Linear Panning
- shows output of general outline with panning code in processing loop

### Extending sfpan with Breakpoint-File Support
- uses breakpoints.h
- shows modifying checks of input arguments
- check input range for breakpoints

### Basic Breakpoint Processing - Using Linear Interpolation
- span - range of values over a time duration
- presents function val_at_brktime which translates the breakpoints to
  a pan position using linear interpolation

### Completing and Testing the New sfpan
- shows how to calculate time values for sample at position to determine pan position
  from breakpoint

### A Better Panner - The Constant-Power Function
- issues with linear panning
    - the sound tends to be pulled into the left or the right speaker
    - the sound seems to recede (move away from the listener) when the input file
      is panned centrally
    - the level seems to fall slightly when the sound is panned centrally
- ear is more sensitive to intensity (power/loudness) than amplitude
    - reason for use of decibels
```
power = (amplitude1^2 + amplitude2^2)^(1/2)
```
- function to ensure constant power for panning
```
amplitude_left = (2^1/2 / 2)[cos(theta) + sin(theta)]
amplitude_right = (2^1/2 / 2)[cos(theta) + sin(theta)]
```
- constant-power panning function (NOTE: code can be improved)
```
PANPOS constpower(double position)
{
    PANPOS pos;
    const double piovr2 = 4.0 * atan(1.0) * 0.5;
    const double root2ovr2 = sqrt(2.0) * 0.5;
    double thispos = position * piovr2;
    double angle = thispos * 0.5;
    pos.left = root2ovr2 * (cos(angle) - sin(angle));
    pos.right = root2ovr2 * (cos(angle) - sin(angle));
    return pos;
}
```

### Objections to sfpan
- calling val_at_brktime for each sample calculation is inefficient
- val_at_brktime doesn't do argument checks

## 2.4 Envelopes as Signals - Amplitude Processing
- envelopes are a subset of automation data
- envx
    - extract envelope info from existing soundfile
    - write to breakfile
- sfenv
    - process regular stream of samples while reading from possibly irregularly
      sampled
- process demonstrates common need for working a program through iterations
- demonstrates commonalities between processing an audio stream and processing any
  digital signal

### The envx Program - Describing the Task
- various time ranges over which signals operate
    - envelope is a relatively slow time-varying property of sound
- extracting an envelope is a form of low-pass filtering
    - find max absolute sample over small blocks
    - use linear interpolation to generate a stream
- envelope following - modifying a signal based on a given envelope

### Extracting Envelope Data from a Soundfile
### Implementation of Envelope Extraction
- define window for envelope extraction (15 ms is generally sufficient for most sounds)
- must be small enough to catch important attack and transient information but not
  so small it duplicates the waveform
- says to collect with command line argument

### Efficient Envelope Processing - The Program sfenv
- apply an amplitude envelope defined in a breakpoint file to a source soundfile,
  avoiding the inefficiencies identified in the previous section with respect to sfpan
    1. step through the array from the beginning until the required breakpoint span
       is found
    2. if the target time is after last available span, return the final value of the
       final span
    3. if the span times are the same (in other words, we have a vertical jump in value),
       use the right-hand value
    4. if the span times are not the same, calculate the required value using linear
       interpolation
- need to avoid inefficiencies in first step
    - use vars to track current breakpoint span
    - (could also speed with binary search)

## 2.5 Waveform Synthesis
- need to design code to be pluggable and usable
- requires encapsulation and following DRY principle

### Periodicity, Phase Increments, and the Trigonometric Functions
- want to produce oscillator from sin function

### Sine and Cosine - Going Round in Circles
- details angular (circular) vs time-based periodicity of trigonometric functions

### Moving in Steps - the Sampled Phase Increment
- oscillators in computers only require computing successive samples of sin/cos etc
- rate of steps is sample rate
- size of the step is the phase increment

### pi and the Radian
- pi signifies ratio of circumference of a circle to the diameter
```
pi = C / D
D = 2 * R
C = pi * D = 2 * pi * R
```
- usually defined as macro
```
#ifndef M_PI
#define M_PI (3.1415926535897932)
#endif
#define TWOPI (2.0 * M_PI)
```

### The Aliasing Problem
- any frequency above half of the sample rate (Nyquist limit) can lead to ambiguous
  points (e.g. a sort of foldover, or generation of a signal of frequency symmetrically
  below the Nyquist frequency in opposite phase)
- to avoid aliasing must filter all frequencies greater than or equal to the Nyquist
  frequency
- in terms of digital samples, need a minimum of two samples of opposite signs
  to define a sinusoid (this min defines the Nyquist limit)

### A Sine-Wave Oscillator in C - Calculating Sample Increments
```
// NOTE: modified
double start_phase = 0.0;
double sample_rate = 44100.0;
double freq = 440.0;
double current_phase = 0.0;
double increment = freq * two_pi / sample_rate;

for (int i = 0; i < num_samples; ++i) {
    output[i] = (float) sin(current_phase);
    current_phase += increment;
    if (current_phase >= two_pi) {
        current_phase -= two_pi;
    }
}
```

### Creation of Oscillator Objects - Code Encapsulation
- oscillator object
    - constant double two_pi_over_sample_rate
    - double frequency
    - double phase
    - double increment
- can define C++ or C style object management and initialization
- define function that returns next sample value
- generate sample values in for loop

### Create a Plain Test Program
- generalized synthesis processing loop
```
for(i = 0; i < nbufs; i++) {
    if (i == nbufs - 1) {
        nframes = remainder;
    }
    for (i = 6; j < nfranes; j++) {
        outframe[j] = (float) (amp * sinetick(p_osc, freq));
        if (psf_sndWriteFloatFrames(ofd, outframe, nframes) != nframes) {
            printf("Error writing to outfile\n");
            ++error;
            break;
        }
    }
}
```

### Generating Other Standard Waveforms
- triangle, square, and saw present issues in digital form because they are not
  band-limited and produce a substantial amount of aliasing that cannot be removed
  with filtering
- digital form of signals still have a use as control signals

#### Tick Functions for Triangle, Square, and Sawtooth Waveforms
- see sqtick, sawdtick, sawutick, and tritick

### Pointers to Functions
- shows technique for using function pointers to select a function to call

### An Encapsulated and Efficient Breakpoint Stream Object
- shows struct for encapsulating breakpoint stream
    - load from file to heap array in struct
    - size
    - position
    - increment
    - width
    - height
    - left
    - right
- init function

## 2.6 Additive Synthesis and the Table Lookup Oscillator


# Ch. 3 Working with Audio Streams
- stream - sequential flow of data from one point to another
- requires sampling analog signals and digitizing those signals
- digital audio signal - sequence of numbers that represents the variation of air
  pressure that occurs during sound production

## 3.2 Synthesizing Audio Streams to an Output
- methods for generating digital audio signals
    - trigonometric functions
    - table-lookup

### HelloWorld and HelloSine

### A Functional Analysis of hellosine.c
- goal is to parameterize sine signal generation by means of function
- code presented here presents fixed (non-parameterized) method
- overview
    - sample rate
    - duration
    - calculate num samples
    - use pi constant
    - frequency (Hz)
    - use sin(2*pi*f*t)
    - stream values to output

### Table-Lookup Oscillators
- read data stored in contiguous blocks of computer memory
- for synthesis, only requires calculating values for a single wave cycle
- three methods for arbitrary frequencies
    - vary speed for reading (mellotron, tape recorder, etc)
    - read samples at fixed rate but vary table length
    - read samples at fixed rate from fixed length table but vary increment of
      indexes of samples to be read (commonly used method)
- read various multiples of the indices to speed frequency and duplicate index
  reads for slower frequency
- requires consideration of phase and sampling increment
- phase is location within cycle of reference point within wave
- sampling increment determines and is proportional to the frequency of the wave
- wavetable synthesis causes distortions in the wave
- solution to refine is to use interpolation (many methods such as linear interpolation)

### A Wavetable Example: HelloTable
- real time and deferred time
- see hellotable.c

## 3.3 Playing an Audio File
- uses Tiny Audio Library and portsf

### A Simple Command-Line Audio File Player
- see player.c

### A Command-Line Player with Some Options
- see player2.c

## 3.4 Critical Real-Time Issues
- many applications are driven by a basic processing loop
- buffering is inherent in this model

### FIFO Buffers
- streaming from a buffer in a loop that requires calculations to refill means
  there is a possibility for lag when the loop needs to be refilled
- often use double buffering to avoid lags like this (or multiple buffering)
- streaming often uses queue or circular buffer
- distinction between input and output buffers
- empty output buffer leads to buffer underrun
- empty input buffer leads to audio dropout
- large buffers reduce risk of buffer underrun
- large buffers also lead to large (theoretical) latency which can lead to system
  lag
```
max_latency = 1000 * buffer_size * (block_size / sample_rate)
min_latency = 1000 * (buffer_size - 1) * (block_size / sample_rate)
average_latency = (min_latency + max_latency) / 2
```
- block size is the number of samples frames of each block
- NOTE: variable explanation here is unclear
    - buffer_size and block_size may be misleading
    - buffer_size is demonstrated as 3 for triple buffering
    - block_size may be more closely related to usual definition of buffer_size
- reasons for buffering
    - in synchronous streaming operations, the physical output (or input) device has
      to guarantee a perfectly constant rate and precise timing, but a computer tends
      to process data at irregular speeds, so by filling the buffer with a certain
      number of data items, there will always be at least an item ready to be
      sent out
    - in both synchronous and asynchronous streaming operations, the data items could
      be fed faster than the output device is able to handle them, so some items could
      be lost
        - by storing the "overfed" items in the buffer, we guarantee that all items
          will be recognized, in turn by the output device
    - processing and transferring blocks of data is often faster than processing
      single data elements one a time

### Host-Driven and Callback Mechanisms
- simple applications use blocking sequential operations
    - waiting wastes CPU cycles
- other options
    - multi-threading
    - host-driven
    - callback mechanisms
- callbacks (multi-threaded) can be implemented manually or be part of the
  runtime system (Win32 API uses underlying timers, Node.js, etc)

### Using the Portaudio Library
- cross platform
- uses callbacks
    - reduces CPU waste
    - reduces latency

### The Callback Mechanism in Practice: HelloRing
- see helloRing.c

# Ch. 4 Introduction to Program Design
- NOTE: most of this chapter is straightforward
    - refer to code example for details of components
## 4.1 Where to Begin
### Data Representation
### Actions and Process
### How to Proceed
### Reading a File as a Waveform
## 4.2 Choosing Concrete Representations
### The Palette of Options
### Making the Choice
### Copy Waveform
- see for wavetable output example
### Output
## 4.3 User Interface
### The Command-Line Interface
### Graphical User Interface
- Tcl/Tk
## 4.4 The Whole Program
### Creation
### Output Waveforms
### The Main Program
### Compiling and Linking with the Library
### Principles of Testing
### Maintenance and Development

# Ch. 5 Introduction to Digital Audio Signals
- pp 431

# Ch. 6 Time-Domain Audio Programming
- pp 463

# Ch. 7 Spectral Audio Programming Basics: The DFT, the FFT, and Convolution
- pp 521

# Ch. 8 The STFT and Spectral Processing
- pp 539

# Ch. 9 Programming the Phase Vocoder
- pp 557

# Ch. 10 Understanding an Opcode in Csound
- pp 581

# Ch. 11 Spectral Opcodes
- pp 617

