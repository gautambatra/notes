# Compilation
* Compiler takes the code, creates object files 
* Linker takes all object files and libraries and links them to create an executable
* The executable format depends on the OS. It contains the machine code. This code is loaded into the virtual memory (see notes [[memory#Virtual Memory]])
* If there are system calls in the code, pointers to the actual OS system calls are also placed in the virtual memory in the code wherever they're called


## TODO
* See if this page can be merged with [[memory]] and [[memory-management]]
