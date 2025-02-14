# Header Files
* Should not be explicitly added to the compile command. Should be included using `#include`
* Best to keep only declarations and avoid definitions in a header file, could result in multiple definitions when included from multiple files.
* Best to include a header in its paired source file. Helps catching errors in compilation rather than linking.
* Use double quotes to include header files that you’ve written or are expected to be found in the current directory. Use angled brackets to include headers that come with your compiler, OS, or third-party libraries you’ve installed elsewhere on your system.
* For modern C++, the header files which declare names in the `std` namespace do not contain the `.h` extension, e.g., `iostream`. The C-compatibility headers that declare names in `std` start with `c` and have no `.h`, e.g., `cstddef`. These are the ones that new programs should use.
    * The older standard header files with `.h` declare the names in the global namespace, e.g., `iostream.h` and `stddef.h`
* When using header files from other directories, they should be present in the `include path` or `search directory`
    * For gcc/clang: `g++ -o main -I/source/includes main.cpp` (no space after `I`)
* Each file should explicitly #include all of the header files it needs to compile. Do not rely on headers included transitively from other headers.
* To maximize the chance that missing includes will be flagged by compiler, order your #includes as follows:
    1. The paired header file for this code file (e.g. `add.cpp` should `#include "add.h"`)
    2.  Other headers from the same project (e.g. `#include "mymath.h"`)
    3.  3rd party library headers (e.g. `#include <boost/tuple/tuple.hpp>`)
    4.  Standard library headers (e.g. `#include <iostream>`)
    * The headers for each grouping should be sorted alphabetically (unless the documentation for a 3rd party library instructs you to do otherwise).
* Always include header guards
* Prefer putting documentation on what something does or how to use it in the header. It’s more likely to be seen there. Documentation describing how something works should remain in the source files.

## Header Guards/ Include guards
* Ensure that a header file is not included multiple times in a file, thus avoiding multiple definitions 
```cpp
#ifndef SOME_UNIQUE_NAME_HERE
#define SOME_UNIQUE_NAME_HERE

// your declarations (and certain types of definitions) here

#endif
```
* Another (modern) way: `# pragma once` at the beginning of the header file. Compiler puts the guards for us. Implementation is compiler specific. Include guards are generally still preferred because `#pragma once` is not part of C++ standard and is not mandatory for a compiler to implement.


## Resources/References
* [learncpp - Header files](https://www.learncpp.com/cpp-tutorial/header-files/)
