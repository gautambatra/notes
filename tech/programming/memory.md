# Memory
* All code is essentially modifying memory. Putting stuff into memory that others can use. Eg., audio/vedio processing code is putting the data in the memory that the speaker/screen then uses to convert to audio/video signals.


## Virtual Memory
* Allows a program to see what appears to be the real memory of the machine, but it's not the real memory in terms of layout.
* Memory is divided into pages, e.g., chunks of 4KB or 64KB. These pages exist in physical memory.
* A process has its own view of the pages and their layout. The pages it accesses only exist when it accesses them.
* The OS shuttles the physical memory to another location (like a hard drive) or rearranges its layout using a table maintained on a CPU. The CPU does the translation so that each process' view of the pages is dynamically mapped to the actual physical locations.
* Each virtual memory page has flags to identify whether the page is readable/writeable/executable by the process. The OS will usually make the executable pages read/write protected for security.
* When a code refers to a memory address, it's referring to the address in the virtual space, which looks like a physical address to the code/program. It could be mapped to different parts in the physical memory. Some of it may not even be in the physical memory at the time because it got swapped out to disk, which when accessed gets swapped back in to memory where the program expects it.
* When the OS needs more physical memory, it can take a page in the physical memory and move it disk. It will unset read/write/exec permissions for this page. Now, if the code tries to read/write/exec that page, a *page fault* will result and an interrupt will be generated, halting the program. The OS will now restore that page from the disk and reload it into virtual memory and resume the program.
* It's good to know about this because we may run into performance issues due to swapping.
* The OS may randomise the starting address for the virtual memory of a program so that it's not the same for every run. Makes it more secure (hackers cannot deterministically attack a memory address).
* CPU accesses instructions from memory as it needs to execute them. Most CPUs have an I-cache which is used to cache some instructions (in its local cache) for efficiency.


### Stack
* OS maps the page to be used as the stack. There are guard pages following the stack page to allow for the stack to grow as and when needed.
    * Automatically allocated by the compiler for high level languages <-- confirm
* Keeps changing depending on the call stack
* Contiguous? In debug mode there will be extra padding between stack frames.


## Physical Memory/Cache
See [[cpu#Caches]]


## References
* [Casey Muratori Intro to C on Windows - Day 3](https://youtu.be/T4CjOB0y9nI?si=tmTwq3e-z1tcp97e)
* [Casey Muratori Intro to C on Windows - Day 5](https://youtu.be/Pb19uCFU2EA?si=Bv867tTlDe5_fZTs)

## TODO
* See if this page can be merged with [[memory-management]] and [[compilation]]
