# Memory Management
## Introduction
* When an OS runs a program, it begins by loading it into memory
* Old style: Segmented Memory
    * OS allocates a continguous block or segment of memory to the program
    * Program divides the memory into regions to perform specific program functions (code, data, heap, stack)
* New style: Paged Memory
    * Memory is managed dynamically. Amount of memory may change depending on program's needs.
    * Memory is allocated and deallocated in fixed-size chunks called *pages*.
    * Initially OS allocates a minimal number of pages to a program, and allocates additional pages as needed
    * Machine code and data are not immediately needed so are not loaded.
    * Program still continues to organize the allocated memory into the same logical/functional units, i.e., code, data, heap, stack, although the physical layout is not organized like this


## Types of Memory Allocation
1. Static: 
    * For global and static variables
    * Happens once at runtime at program startup
    * Size of variable/array must be known at compile time
    * Automatically deallocated when program terminates
1. Automatic: 
    * For local variables and function parameters
    * Automatically allocated when relevant scope is entered, freed when exited
    * Size of variable/array must be known at compile time
1. Dynamic

## Memory Areas/Segments
Program memory is divided into the following segments:
* Code segment
    * aka text segment
    * stores compiled program
    * typically read-only
* bss segment
    * aka uninitialised data segment
    * stores zero-initialised global and static variables
* data segment
    * aka initialised data segment
    * stores initialised global and static variables
* heap
    * aka free store
    * dynamically allocated variables get memory from this segment
    * for details on dynamic memory allocation see notes [[dynamic-memory-allocation]]
* call stack
    * aka stack
    * stores function parameters, local variables, and other function related information
    * keeps track of all active functions
    * implemented as a stack data structure
        * stack frame: an entry in the stack, i.e., all data associated with one function call:
            * return address, i.e., the address of the instruction beyond the function call (where to return after function exits)
            * all function arguments
            * memory for any local variables
            * saved copies of any registers modified by the function that need to be restored when the function returns
        * marker/stack pointer (SP): keeps track of the top of the call stack
        * depending on the architecture, new stack frames pushed may have a higher or lower memory address than the previous top
        * optimization: need not deallocate/clear a stack frame on a pop operation. Can just move the SP
        * When a function call is encountered:
            1. stack frame is created and pushed to call stack
            2. CPU jumps to start of called function
            3. Instructions inside the function are executed
        * When a function returns
            1. Registers are restored from the call stack
            2. stack frame is popped off the call stack (local variables/registers are freed)
            3. Return value is handled. Depending on architecture, it could be part of the stack or saved in a register
            4. CPU resumes execution at return address
    * All memory allocated on stack is known at compile time
      at compilation time, the compiler will lay out where all of the normal variables are in memory as offsets from some base address (that will be provided at runtime). So the compiler will say x is at (base address + 4), y is at (base address + 8), etc... Then wherever the compiler sees x in our source code, it generates compiled code that uses (base address + 4) instead. That way the compiler doesn't have to worry about where exactly in memory x should live, it just needs to make sure that all the variables used are separated enough so they don't overlap, and the actual final addresses are calculated at runtime. For example, when the function containing the variables is called, the base address gets assigned -- let's say at memory address 136. And since the compiler put x at (base address + 4), the executable code will access memory address (136 + 4), which is memory address 140
    * Stack overflow: when all memory in the stack has been allocated. Usually the stack size is a few MB's (1MB in Visual Studio for Windows, upto 8MB in g++/Clang for Unix)
        * too many variables/too many nested function calls can cause it
        * usually leads to access violation and program termination

## Destructors
* Object is destroyed *after* destructor executes, which means a destructor can safely call other member functions

## RAII - Resource Acquisition Is Initialization
* Resource use is tied to the lifetime of objects with automatic (non-dynamic) duration
* Implemented in C++ via class constructors and destructors: a resource (memory/file handle/mutex/etc) is typically acquired in the object's constructor and released in the destructor
* Resource holding objects are thus cleaned automatically, preventing memory leak

## Bits, bytes, and memory addressing
* Smallest unit: *binary digit*, aka *bit*
* Memory is organised into *memory addresses*, each holding 1 *byte* of data, which is 8 bits as per modern standards.
* In C++ we work with byte-sized chunks of data

## Fundamental data type sizes
* Each type must occupy at least 1 byte
* `char` and `char8_t` are exactly 1 byte
* Only minimum sizes are defined by the standard: 
    short: 2
    int: 2
    long: 4
    long long: 8
    float: 4
    double: 8
    long double: 8

## Resources/references
* [learcpp - stack and heap](https://www.learncpp.com/cpp-tutorial/the-stack-and-the-heap/)
* [Weber university book: Memory Management: Stack And Heap](https://icarus.cs.weber.edu/~dab/cs1410/textbook/4.Pointers/memory.html)
* [learcpp - destructors](https://www.learncpp.com/cpp-tutorial/destructors/)


## TODO
* See if this page can be merged with [[memory]] and [[compilation]]
