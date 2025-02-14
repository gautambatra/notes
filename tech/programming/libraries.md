# Libraries
* A package meant to be used by many programs
* Don't change frequently so don't need to be recompiled
* In machine code format - closed source if needed

## Static Libraries
* Aka archive
* Routines compiled and linked directly into the main program
* Extensions: Windows: .lib, Linux: .a (archive)
* Ensures right version is compiled for the main program
* Part of the executable - exe size increases
* Entire executable needs to be recompiled if library has to be updated

## Dynamic Libraries
* Routines loaded at program run-time
* Not part of the executable, can be updated independently
* Extensions: Windows: .dll (dynamic linked library), Linux: .so (shared object)
* Many programs can share one copy
* Main program needs to load the library and interface with it

## Import Library
* A static library that automates loading and using a dynamic library
* Windows: .dll of the same name, Linux: .so is both the dynamic and import library
* Compilers can create an import library for a dynamic library

## Installing and Using Libraries
1. Download the library
2. Install from source or using a package manager
3. Make sure the header files of the library are accessible (should be part of the include file search path)
    * Windows: include subdirectory of the library install path
    * Linux: /usr/include directory
4. Tell thel linker where to look for the library files (should be part of library search path)
    * Windows: lib subdirectory of the library install path (after cwd, directories set by calling `SetDllDirectory()`, Windows, System32, directories in PATH)
    * Linux: /usr/lib directory (after directories in LD_LIBRARY_PATH)
5. For static libraries, tell the linker which libraries to link
6. Include the library's header files in the main program

# References
* [learcpp - Static and dynamic libraries](https://www.learncpp.com/cpp-tutorial/a1-static-and-dynamic-libraries/)
