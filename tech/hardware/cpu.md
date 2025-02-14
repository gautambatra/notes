# CPU
## Introduction
* CPU is the physical processor, the chip with a heat sink built on it. Does all the execution - graphics, networking, etc. 
* Internally divided into multiple *cores*, which are logical processing units that can run code independently. Each core is essentially capable of doing what a legacy single core CPU could do and has it's own units for doing maths, memory addressing, etc.
* Cores are further subdivided into *hyperthreads*. Even though a core can do one thing at a time, it can keep track of multiple *states*. To the OS each of these states/threads looks like a logical processing unit or a processor.


## Registers
* Each (assembly) instruction boils down to:
    * Getting some memory
    * Pulling memory into a register
    * Operating on those registers and change their values
    * Putting the updated values back to the meory
* Each CPU has a fixed (not very large) number of registers 


## Metrics
* Latency: How long it takes for one instruction/request/thing to go do it's job and come back.
* Throughput/Bandwidth: How many things we can get back in 1 second at peak congestion (when the "things" are pipelined/queued).


## Caches
* Pieces of memory on the chip that have copies of things from the main memory (or what's going to be in the main memory)
* Each core has its own L1 and L2 cache
* L1 - smallest, closest to the core, L2 bigger, little farther away
* L3 - largest, farthest. Many times shared among all cores.
* If something is not present in any of the caches (L1/L2/L3), the CPU looks in the memory. Memory controller does the communication with the memory.
* Some data may even be moved to the hard drive and fetched when needed (s l o w e s t).


## Assembly
Depending on the type of processor, all instructions may be of the same size in memory, e.g., powerpc, or of variable sizes, e.g., intel.


## Endianness
* Little endian: low order byte is stored first in memory (i.e., if the lowest order byte is stored in memory address x, then the next higher order byte is stored in memory address x + 1, and so on)
* Big endian: higher order byte is stored first in memory
* x86, x64, arm are little endian architecture
* In programming, this matters when you are loading pre-stored data from a disk which has a different endianness than the machine on which the code is running. If that's the case the code needs to do the swapping for the data to make sense. Examples:
    * The data may have been packaged while compiling the binaries, and may be getting loaded while running them on another machine with a different endian architecture. 
    * A file being read may be in a file format that has a different endianness than the machine on which it is being loaded.
* On a little endian machine where sizeof(int) is 4 and sizeof(short) is 2, intPtr and shortPtr below would have the same value:
```cpp
int x = 16;
int* intPtr = &x;
short* shortPtr = &x;
bool littleEndian = *intPtr == *shortPtr;
```


## Making the CPU run our program faster
* Two main ways:
    1. Reduce the number of instructions fed to the CPU
    2. Increase the speed of the instructions
        *  Different instructions take different number of clock cycles.
        *  We can try to change:
            * Which instructions we feed the CPU
            * Order of the instructions
            * How the instructions access the memory

### Waste code
* When writing high level code, keep in mind the instructions that will ultimately be fed to the CPU, i.e., what that high level code turns into.
* An interpreted language like python will perform more than a hundred assembly instructions just to achieve a single add instructions. This is because the interpreter code is the actual code that runs the python code, and it parses the instructions, decodes them, and does semantic/type checks, i.e., it generates a lot of "waste code".
* On the other hand, even an unoptimised C code will produce just one assembly instruction per add.

### Instructions Per Clock (IPC), Instruction Level Parallelism (ILP)
* IPC: average number of instructions per clock cycle
* ILP: number of instructions that can be done simultaneously
* Each CPU has a maximum IPC it can do. If we are not hitting that with our program, there's an opportunity for increasing the performance.
* Modern CPUs can go upto 6-8 instructions per clock

* **Serial dependency chain**
    ```cpp
    for (int i = 0; i < count; ++i)
    {
        sum += arr[i];
        sum += arr[i+1];
    }
    ```
    ```
    ADD sum, arr[i]
    ADD sum, arr[i+1]
    cmp i, count
    ADD sum, arr[i+2]
    ADD sum, arr[i+3]
    ...
    ```
    * In the above code, the compare and add can happen together because they're independent, but two adds cannot happen together as we need the previous result in the current add. This is a *serial dependency chain*, which is a chain of things dependent on each other.
    * CPU has no way of knowing these adds are associative and commutative, but the programmer does and can improve upon the loop. Example:
    ```
    ADD a, arr[i]
    ADD b, arr[i+1]
    cmp i, count
    ADD a, arr[i+2]
    ADD b, arr[i+3]
    ...
    ADD a, b
    ```
    Here, the two sums in each iteration now become independent and the CPU can do them together.

### Single Instruction Multiple Data (SIMD)
* Operating on multiple pieces of data in a single instruction
* Makes use of wide registers to combine multiple operations on different data into a single instructions
* *Kind of* like working with vectors
* E.g.: SSE instruction set in x64 (supported in almost every chip)
    PADDD (Packed, ADD, D-word)
* Other SIMD instruction set families: AVX (A little less support), AVX512 (very little support)
    SSE - 4 times wide, AVX = 8, AVX512 = 16
        These numbers are for 32 bit value. If using 16 bit values, these numbers will double.
        So basically, the wide register in SSE is 128 bits long, AVX is 256 bits, and so on.
* The number of additions will be the same as original, but only one instructions will have to be decoded and issued.
* We can also combine this with breaking the dependency chain by increasing the IPC as in the previous section, i.e., combine data parallelism and instruction parallelism

### Caching
* load = read, store = write
* load must be analysed as they affect performance
* In `ADD A arr[i]`, arr[i] is "loaded", so if it's slow the add will stall until it's done
* Loading from main memory is much slower as compared to operations like ADD. Enter cache.
* Register file: designed to produce values extremely quickly and feed them to instructions. Extremely fast, and very small in size.
* When loading a value, CPU will first check if the desired memory location is available in the L1 cache, a small memory directly on the CPU. Extremely fast, around 3-4 cycles. Typical L1 size is a few KB, e.g., 32K.
* If not found in L1, CPU checks L2 cache. It's slightly bigger than L1 (~256K), and slower (~14 cycles)
* L3 cache: much bigger than L2 cache (~8MB), slower(~80 cycles).
* Each core has its own L1 and L2 caches. L3 is shared.
* If not found in any of the caches, main memory is read, which is very large, and very slow (> 100 cycles).
* If your code does all the work from the main memory, it will be really slow. In that case, try to do bigger and fewer reads.
* Larger data sets/buffers may result in reads from L2 and L3 caches, or in the worst case, memain memory, slowing the program down significantly. 

### Multithreading
* A standard modern CPU has more than one *cores* on it, which can run instructions independently, parallely. Each core has a separate execution pipeline. Depending on the cpu, there could be anywhere from 4 to 96 cores.
* Core is the actual hardware. *Threads* are an OS concept representing an independent execution stream, to be run on a separate core.
* If we can break a problem into multiple independent operations, we can take advantage of this
* Note that there is some overhead in starting the threads and running them on cores, so if the workload per thread is not large you won't see much improvement in performance.
* If the workload is large and involves memory interactions, the performance gains with multithreading will be even more significant. L1 and L2 caches will be separate, so effective cache size increases (n times, where n is the number of cores).
* Note: a chip may be designed to handle single-threaded workloads, with multithreading only used for offloading tasks like computation. In this case, that single core uses a lot of memory bandwidth and there isn't much memory bandwidth to spare for other cores. In such cases the benefit of multithreading will not be proportional to the cores count.
* Apart from preventing waste code, multithreading is probably the biggest performance multiplier, because the number of cores per machine/server is constantly increasing.

### Notes
* Most of the times while decoding instructions, the CPU needs to read one or more bytes and then make a decision whether it needs to read more bytes for this instructions. In other words, it doesn't know when this instruction ends and the next one begins before reading a few bytes. This makes decoding multiple instructions per clock a much harder problem than if the instruction size were fixed.
* Instruction writing/decoding is also made complex due to the fact that not all registers have the same functionality. For example, in 8086, for ax, bx, cx, and dx we can access the lower/higher byte, e.g., al, bh, etc., but for other registers we can't. Also, some registers can be used for specifying memory addresses and offsets, such as bp, bx, si, di, etc., but others can't.

## References
* [Casey Muratori - Intro to C on Windows - Day 1](https://youtu.be/F3ntGDm6hOs?si=P964JkO40Lo6mO4f)
* [Casey Muratori - Intro to C on Windows - Day 3](https://youtu.be/T4CjOB0y9nI?si=yqURBkRC9wLtQTh1)
