# Code Performance

## Assembly Language
Looking at assembly language for performance has the following benefits:
* See which assembly language instructions the compiler chose for your high level program to get a sense of whether it's a good choice performance-wise.
* For optimising your code for specific architectures, hardware, etc.
* Can estimate the best case scenario of the performance in terms of cpu cycles, based on the number of different type of instructions in the assembly code. Helps to know how far we are from the best case.

### Estimating the Performance
* The spec of the architecture mentions the number of clocks/cpu cycles needed for each type of instruction.
* Add up the individual values to get an estimate of your routine. Remember, this is just an estimate, not the real value.
* There may be penalties associated with some instructions on specific architectures
* Other microarchitecture stuff also adds penalties, eg., how the bus is interrupted, instruction fetch, etc.


## Measuring Performance
* Coarse measurements of total program runtime are not very helpful, we need to be more granular.
* Helps us pin-point problem areas and iterate faster

### Time Measurement
* In 8086 there wasn't any instruction for time measurement, CPU didn't keep time. Had to rely on other clocks on the motherboard.
* Pentium (586) introduced RDTSC instruction (Read Time Stamp Counter). No operands.
    * Writes a 64 bit value across 2 32-bit registers
    * The 64 bit number is a measure of the time (CPU cycles) since CPU started. May also set a custom initial value instead of the default, which is 0.
    * If we know that the code we're profiling won't take more than 2^32 (~4 billion) cycles, we can just use the lower 32-bit register to avoid the overhead of shifting + ORing to get the 64-bit value 
