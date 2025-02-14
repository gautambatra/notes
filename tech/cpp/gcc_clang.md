# GCC and CLANG Compilers

## Optimization Options
* `-O0` is the recommended optimization level for debug builds, as it disables optimization. This is the default setting.
* `-O2` is the recommended optimization level for release builds, as it applies optimizations that should be beneficial for all programs.
* `-O3` adds additional optimizations that may or may not perform better than -O2 depending on the specific program. Once your program is written, you can try compiling your release build with -O3 instead of -O2 and measure to see which is faster.


## Compiler Extensions
* Allow the compiler to produce non-standard code, or code that is compatible with older versions of the language, etc.
* Disable using the `-pedantic-errors` flag 

## Warnings
* Increasing the warning level: `-Wall -Weffc++ -Wextra -Wconversion -Wsign-conversion`
* Treat warning as errors: `-Werror`

## Setting the Language Standard
`-std=c++11`, `-std=c++14`, `-std=c++17`, `-std=c++20`, or `-std=c++23`

## Compiler Support for C++ Features
* [cppreference - compiler support](https://en.cppreference.com/w/cpp/compiler_support )

## Specifying Include directory
`g++ -o main -I/source/includes main.cpp`

## Emitting Assembly Code To stdout
`g++ -g -c -Wa,-alh foo.cpp`
* -g -Wa,-alh will give intermixed source and assembly on stdout
* -Wa: ask compiler driver to pass options to assembler
* -al: turn on assembly listing
* -ah: add "high-level source" listing


## Output Assembly Code to File
* `-S` : output something.s file with assembly code
* `-masm=intel` : choosing assembly flavour to output
* `-fverbose-asm` : put extra comments on the assembly output to make it more readable


## Peak into a binary
`objdump -d a.out`


## Resources/References
* [learncpp - Configuring your compiler: Build configurations](https://www.learncpp.com/cpp-tutorial/configuring-your-compiler-build-configurations/)
* [learncpp - Configuring your compiler: Compiler extensions](https://www.learncpp.com/cpp-tutorial/configuring-your-compiler-compiler-extensions/)
* [learncpp - Configuring your compiler: Warning and error levels](https://www.learncpp.com/cpp-tutorial/configuring-your-compiler-warning-and-error-levels/)
* [learncpp - Configuring your compiler: Choosing a language standard](https://www.learncpp.com/cpp-tutorial/configuring-your-compiler-choosing-a-language-standard/)
* [learncpp - What language standard is my compiler using?](https://www.learncpp.com/cpp-tutorial/what-language-standard-is-my-compiler-using/)
