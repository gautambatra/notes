# Code Optimization
## Compiler Optimizations
1. Constant folding
    * compile time
    * Simple constant folding is performed by front end
    * Example:

      ```cpp
      int main {return 2+3;}
      ```
      Assembly code output:
      ```asm
      PUBLIC  main
      _TEXT   SEGMENT
      main    PROC
      mov     eax, 5
      ret     0
      main    ENDP
      _TEXT   ENDS
      END
      ```   
      `mov eax, 5` EAX register is used to set the `int` value that a function returns
      `ret 0` means return the control to the caller and pop 0 bytes from the stack

2. Constant Propagation
*  Replace variables known to have constant values with their values
*  Reduces memory access, leading to better performance
```cpp
int main()
{
	int x { 7 };
	std::cout << x << '\n'; // optimised to std::cout << 7 << '\n';

	return 0;
}
```
* Using const variables can help the compiler optimize more effectively. If x was declared as const, there is more chance of the code being optimised.

3. Dead code elimination
* Remove code that may be executed but has no effect on the program’s behavior
* The above example will be optimised as follows:
```cpp
int main()
{
	std::cout << 7 << '\n'; // declaration of x not required anymore
	return 0;
}
```

## Random Info
### [Microsoft Optimization Article](https://learn.microsoft.com/en-us/cpp/build/optimizing-your-code?view=msvc-170https://learn.microsoft.com/en-us/cpp/build/optimizing-your-code?view=msvc-170)
> optimizing a program for speed could cause code to run slower. This is because some optimizations for speed increase code size. For example, inlining functions eliminates the overhead of function calls. However, inlining too much code might make your program so large that the number of virtual-memory page faults increases. Therefore, the speed gained from eliminating function calls may be lost to memory swapping.

## Techniques for Optimising C++ Code
* Inline Functions
    * Suggestion to the compiler to replace function call with actual function body
    * Eliminates funcction call overhead
    * To confirm: definition must have the inline keyword, declaration may or may not have it
    * Tradeoff: code size increases. Larger executables tend to be slower (due to not fitting as well in memory caches).
    * Compiler does a cost/benefit analysis and then decides whether to inline a function call
    * Should be used for short functions (eg functions used for member access) - short functions are sensitive to function call overheads.
    * Following are avoided:
        * Function calls (including parameter passing and placing the object's address on the stack) 
        * Preservation of caller's stack frame
        * New stack frame setup
        * Return-value communication
        * Restoring the old stack frame
        * Return

    * A constexpr/consteval declaration is implicitly inline
    * A function defined entirely inside a class (member or friend function) is implicitly inline
    * A function whose definition is in another translation unit cannot be expanded inline since the compiler cannot see the definition.
    * Historically, compilers couldn't determine whether inlining would be beneficial, so `inline` keyword was added to give the compiler a hint that it would be beneficial.
    * In modern C++:
        * Using `inline` is a form of premature optimization and its misuse may be counterproductive
        * Inline keyword is used at a function definition level, but each call is assessed separately, and some calls of the same function may be expanded inline while others may not.
        * Meaning of `inline` has evolved to mean "multiple definitions allowed"
        * Best practice: do not use `inline` to request expansion. Avoid the use of the inline keyword unless you have a specific, compelling reason to do so (e.g. you’re defining those functions or variables in a header file).
    * There may be more than one definition of an inline function or variable in the program 
        * Each definition must appear in a different translation unit and (for non-static inline functions and variables) all definitions must be identical.
        * It must be declared inline in every translation unit.
        * It has the same address in every translation unit.
        * Inline functions are typically defined in header files, so that including them in different tranlation units results in the exact same definition
    * Because the meaning of the keyword inline for functions came to mean "multiple definitions are permitted" rather than "inlining is preferred", that meaning was extended to variables. 
        * Static constexpr data members are implicitly inline
        * Use inline constexpr variables defined in a header file for global symbolic constants (only works C++17 onwards)
    * Macro vs inline function:
        * macro is always expanded inline, inline functions only expanded if compiler decides it's benificial
        * macros are not type safe
        * macros may lead to unexpected behaviour, see example below
        * Example:
          ```cpp
          #define mult1(a, b) a * b
          #define mult2(a, b) (a) * (b)
          #define mult3(a, b) ((a) * (b))
          
          inline int multiply(int a, int b)
          {
              return a * b;
          }
          
          int main()
          {
              std::cout << (48 / mult1(2 + 2, 3 + 3)) << std::endl; // outputs 33
              std::cout << (48 / mult2(2 + 2, 3 + 3)) << std::endl; // outputs 72
              std::cout << (48 / mult3(2 + 2, 3 + 3)) << std::endl; // outputs 2
              std::cout << (48 / multiply(2 + 2, 3 + 3)) << std::endl; // outputs 2
          
              std::cout << mult3(2, 2.2) << std::endl; // no warning
              std::cout << multiply(2, 2.2); // Warning : conversion from 'double' to 'int', possible loss of data
          }
          ```
        * In some cases macro expressions may be evaluated multiple times when not intended:
          ```cpp
          #define sqr(a) ((a) * (a))
          
          int increment(int& number)
          {
              return number++;
          }
          
          inline int square(int a)
          {
              return a * a;
          }
          
          int main()
          {
              int c = 5;
              std::cout << sqr(increment(c)) << std::endl; // outputs 30 (5 * 6)
              std::cout << c << std::endl; // outputs 7 (increment called twice)
          
              c = 5;
              std::cout << square(increment(c)) << std::endl; // outputs 25
              std::cout << c; // outputs 6
          }
          ```

* [Assumption: `[[assume]]`](https://en.cppreference.com/w/cpp/language/attributes/assume)
    * acts as hint to the compiler to be able to make optimizations
    * Use with caution as if the assumption breaks the behaviour is undefined
    * Example:
    ```cpp
    int main(int p)
    {
       switch(p)
       {
       case 1:
          func1(1);
          break;
       case 2:
          func1(-1);
          break;
       default:
          [[assume(0)]];
          // This tells the optimizer that the default
          // cannot be reached. As so, it does not have to generate
          // the extra code to check that 'p' has a value
          // not represented by a case arm. This makes the switch
          // run faster.
       }
    }
    ```

* Use inline assembly code
    * Can optimise time and memory
    * Can be hardware specific, so may not be portable
    * [asm declaration](https://en.cppreference.com/w/cpp/language/asm)
    * [What is the difference between `asm`, `__asm` and `__asm__`?](https://stackoverflow.com/questions/3323445/what-is-the-difference-between-asm-asm-and-asm)
    * [Microsoft Inline Assembler](https://learn.microsoft.com/en-us/cpp/assembler/inline/inline-assembler?view=msvc-170)
    * [Detailed stackoverflow answer about inline assembly with resources](https://stackoverflow.com/questions/34520013/using-base-pointer-register-in-c-inline-asm/34522750#34522750)

* Use move semantics

## Compile-time Programming
* Use of language features that result in compile-time evaluation
* Makes programs smaller and faster at the cost of slightly larger compile times
* Compiler fails if a code written for compile-time evaluation cannot be executed at compile-time
* Compiler fails for undefined behaviour cases
* Leads to performant, less buggy, and safer code
* Uses the following:
    - constexpr variables
    - constexpr functions
    - templates
    - static_assert
```cpp
const int x { 3 + 4 }; // constant expression 3 + 4 must be evaluated at compile-time
int y { 3 + 4 };       // constant expression 3 + 4 may be evaluated at compile-time or runtime
```
* The likelihood that an expression is fully evaluated at compile-time can be categorized as follows:
    * Never: A non-constant expression where the compiler is not able to determine all values at compile-time.
    * Possibly: A non-constant expression where the compiler is able to determine all values at compile-time (optimized under the as-if rule).
    * Likely: A constant expression used in a context that does not require a constant expression.
    * Always: A constant expression used in a context that requires a constant expression.

### Constant Expressions
* Evaluatable at compile-time
* May be evaluated at compile-time by the compiler
```cpp
int getNumber()
{
    std::cout << "Enter a number: ";
    int y{};
    std::cin >> y; // can only execute at runtime

    return y;      // return value only known at runtime
}

int five()
{
    return 5;      // return value known at compile-time
}

int main()
{
    // Literals can be used in constant expressions
    5;                           // constant expression
    1.2;                         // constant expression
    "Hello world!";              // constant expression

    // Most operators that have constant expression operands can be used in constant expressions
    5 + 6;                       // constant expression
    1.2 * 3.4;                   // constant expression
    8 - 5.6;                     // constant expression (even though operands have different types)
    sizeof(int) + 1;             // constant expression (sizeof can be determined at compile-time)

    // Calls to non-constexpr functions can only be used in runtime expressions
    getNumber();                 // runtime expression
    five();                      // runtime expression (even though return value is constant expression)

    // Operators without constant expression operands can only be used in runtime expressions
    std::cout << 5;              // runtime expression (std::cout isn't a constant expression operand)

    // Const integral variables with a constant expression initializer can be used in constant expressions:
    const int a { 5 };           // a is usable in constant expressions
    const int b { a };           // b is usable in constant expressions (a is a constant expression per the prior statement)
    const long c { a + 2 };      // c is usable in constant expressions (operator+ has constant expression operands)

    // Other variables cannot be used in constant expressions (even when they have a constant expression initializer):
    int d { 5 };                 // d is not usable in constant expressions (d is non-const)
    const int e { d };           // e is not usable in constant expressions (initializer is not a constant expression)
    const double f { 1.2 };      // f is not usable in constant expressions (not a const integral variable)

    return 0;
}
```

## `constexpr`
* Makes the variable/function usable in a constant expression
* Always a compile-time constant
* Works for non-integers as well (`const` does not)
* Any constant variable whose initializer is a constant expression should be declared as constexpr.
* Any constant variable whose initializer is not a constant expression (making it a runtime constant) should be declared as const.
* Function parameters cannot be declared as `constexpr` as their initial value cannot be determined at compile-time
```cpp
int five()
{
    return 5;
}

int main()
{
    constexpr double gravity { 9.8 }; // ok: 9.8 is a constant expression
    constexpr int sum { 4 + 5 };      // ok: 4 + 5 is a constant expression
    constexpr int something { sum };  // ok: sum is a constant expression

    std::cout << "Enter your age: ";
    int age{};
    std::cin >> age;

    constexpr int myAge { age };      // compile error: age is not a constant expression
    constexpr int f { five() };       // compile error: return value of five() is not a constant expression

    return 0;
}
```


## Random Tips (TODO: organise into existing/new sub-headings)
* Pass fundamental types, enums, and string_view by value
* Favour passing by const reference unless the argument needs to be modified.
* Favour pass by (const) reference for types that are expensive to copy
* Prefer passing strings using std::string_view (by value) instead of const std::string&, unless your function calls other functions that require C-style strings or std::string parameters.
* Use pass by reference instead of returning an expensive-to-copy object by value
* Use move semantics: 
    If the argument is an rvalue, it's not expected to be valid after the assignment/copy, so moving ownership is better as it is cheaper (**optimization opportunity**)
* Copy and swap idiom is less performant than the classic approach
* Debug builds should have optimizations turned off. Optimised code is be harder to debug because it's different than the original source code.
* Use constexpr and const when possible, prefer constexpr
* Favour the prefix versions, as they are more performant and less likely to cause surprises.


## Misc
* Doing integer division (or integer modulus operation) is *extremely* expensive for the x64 CPU if the operands are not known at compile time. See [this awesome discussion](https://youtu.be/RrHGX1wwSYM?t=1726) between Primeagen and Casey.
* "Clean code" may not be performant (See "Clean Code, Horrible Performance" by Casey Muratori)
    * Usually, "Clean Code" proponents recommend:
        * Use polymorphism, not if/switch
        * Never know internals of something you're working on
        * Functions should be small
        * Functions should do 1 thing
        * DRY (only this is reasonable, the other 4 are not always true, especially if you're after performance)
    * Prot tip from Casey: when analysing an algorithm, see what operations the CPU would be performing to achieve it, find the latency/throughput numbers for that CPU and estimate the best case performance. This will never be achievable, but will give you an idea about how far your current implementation is from it.



## References
* [Cppreference - inline](https://en.cppreference.com/w/cpp/language/inline)
* [Microsoft - inline functions (must read)](https://learn.microsoft.com/en-us/cpp/cpp/inline-functions-cpp?view=msvc-170)
* [learcpp - The as-if rule and compile-time optimization](https://www.learncpp.com/cpp-tutorial/the-as-if-rule-and-compile-time-optimization/)
* [learcpp - Constant expressions](https://www.learncpp.com/cpp-tutorial/constant-expressions/)
* [learcpp - introduction to smart pointers and move semantics](https://www.learncpp.com/cpp-tutorial/introduction-to-smart-pointers-move-semantics/)
* [learcpp - Inline functions and variables](https://www.learncpp.com/cpp-tutorial/inline-functions-and-variables/)
* [learcpp - Sharing global constants across multiple files (using inline variables)](https://www.learncpp.com/cpp-tutorial/sharing-global-constants-across-multiple-files-using-inline-variables/)
* [Primeagen - Language performances are junk](https://youtu.be/RrHGX1wwSYM?si=xui57o3lgS0hki0h)
* [Casey Muratori - Clean Code, Horrible Performance](https://www.youtube.com/watch?v=tD5NrevFtbU)
* [Casey Muratori - Simple Code, High Performance](https://www.youtube.com/watch?v=Ge3aKEmZcqY)
