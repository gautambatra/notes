# Preprocessor
* Used to be a separate program, now it's built into the compiler
* Does the work in-memory or in temporary files. Original sources are not changed.
* Produces the translation unit, which is then compiled by the compiler (contains no directives)
* Doesn't understand C++ syntax, has its own syntax
* Does the following:
    - Strip out comments
    - Ensures each statement ends with a newline
    - Processes preprocessor directives
* Translation: process of pre-processing, compiling, and linking. See [cppreference - translation phases](https://en.cppreference.com/w/cpp/language/translation_phases) for details.

## Preprocessor Directives
* AKA directives
* Start with `#`, end with newline, not `;`
* Perform text manipulation
* The `using` directive is not a preprocessor directive, so it's not processed by the preprocessor
* `#include`: replaced with the text of the file it includes (including any recursive `#include`s)
* `#define`: used to define macros
    * Object like macros with substitution text:
      ` #define MY_NAME "Gautam"`
      Should be avoided.
    * Object like macros without substitution text:
      `#define TIMEZONE_IST`
    * Processed from top to bottom and remain defined till the end of the file
* Conditional compilation: `#ifdef`, `#ifndef`, `#endif`
  * Can also be written as `#if defined(MACRO_NAME)` and `#if !defined(MACRO_NAME)`
  * Can use `#if 0` / `#endif` to exclude code from compilation
      


## References
* [learncpp - Introduction to local scope](https://www.learncpp.com/cpp-tutorial/introduction-to-the-preprocessor/)
