# Assert
## Definitions
* **Precondition**: a condition that must be true prior to the execution of a block of code (usually accompanied with an early return)
* **Invariant**: a condition that must be true during the execution of a block of code, e.g., the condition in a loop
* **Postcondition**: a condition that must be true after the execution of a block of code
* **Assertion**: an expression that will be true unless there is a bug in the program

# `assert()`
* Preprocessor macro, defined in `<cassert>`
* Usage: `assert(condition)`
* Prints an error message and calls `std::abort()` if the expression in the argument is false. Usually the message prints the file and line number along with the expression that failed.
    * Can add a string literal to make the message more descriptive, eg: `assert(found && "entry not found in database");`
* The condition check is an overhead, asserts should be avoided in production code
    * If macro `NDEBUG` is defined, asserts are disabled throughout the codebase
    * To manually disable asserts in a specific translation unit, add `#define NDEBUG` in that file *before any includes*
* The condition should have no side effects, otherwise debug and production behaviour would not be identical

## `static_assert()`
* Checked at compile-time. Causes compilation error on failure.
* Not a preprocessor macro but a keyword. No header required.
* Usage: `static_assert(condition, diagnostic_message)`
* Condition must be a constant expression
* Diagnostic message is optional C++17 onwards
* Not disabled on release builds
* Should be preferred over `assert()`

## References
* [learncpp - assert and static assert](https://www.learncpp.com/cpp-tutorial/assert-and-static_assert/)
