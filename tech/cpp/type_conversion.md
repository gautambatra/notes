# Type Conversion
* Doesn't change the original value. Uses direct initialization to produce a new value of the desired type. 


## Implicit Type Conversion
* AKA *automatic type conversion*
* Performed by the compiler in the following scenarios:
    - Initialization
    - Function returns
    - Argument passing
    - For some binary operators, eg: `double div {4.0/3};`
    - Using non-boolean in an if condition
* Unsafe implicit conversions, e.g., float to int will result in a compiler warning as there may be loss of data (narrowing conversion)
* Safe conversions/value-preserving conversions: those where no data loss can occur for any value
* Brace-initializations do not allow narrowing conversions
* C++ standard defines how fundamental types can be converted to other types, aka `standard conversions`. 4 categories:
    * Numeric promotions
    * Numeric conversions
    * Arithmetic conversions
    * Other conversions
* If the original value is a `constexpr`, compiler can determine if the conversion is narrowing, and will give a compilation error if there is loss of data
    * Floating point to integral type conversions are always considered narrowing by the compiler irrespective of data loss
    * `constexpr` floating type to narrower floating type is not considred as a narrowing conversion even if there is loss of data

### Numeric promotion
* Type conversion of a narrower numeric type, e.g., `char` to a wider type, e.g., `int` for more efficient processing
* Some 32-bit systems may process 32 bit types more efficiently/faster than, say, 8-bit types, or may not even be able to process types with less than 32 bits.
* Safe conversion

#### Floating point promotion
`float` to `double`

#### Integral promotion
* Conversion to `int` or `unsigned int ` depending on whether `int` can hold the entire range of the original type
* On most modern systems (int is at least 4 bytes):
    `bool`/`char`/`signed char`/`unsigned char`/`signed short`/`unsigned short` to `int` 
* Value-preserving but not guaranteed to be sign-preserving


### Numeric conversion
* 5 types
    * One integral type to another integral type (excluding numeric promotion), e.g., `int` to `short`, `short` to `char`, `int` to `long`, etc.
    * One floating point type to another floating point type, e.g., `double` to `float`, `long double` to `double`, etc.
    * Floating point type to an integral type
    * Integral type to floating point type
    * Integral or floating point type to bool
* 3 Categories
    * Value-prserving, e.g., `int` to `long`, `short` to `double`, etc. Can be converted back to original value.
    * Reinterpretive: unsafe, value may be different but no data loss, e.g., `signed int` to `unsigned int`. May not result in compiler warning. Can be converted back to original value.
    * Lossy, e.g., `double` to `int`, `double` to `float`, etc. Compilers generally issue warning. Cannot be converted back to the original value. 


### Arithmetic conversion
* Operators that require operands of the same type (if not same, implicit conversion occurs as per *usual arithmetic conversion rules*)
    * Binary arithmetic operators: `+`, `-`, `*`, `/`, `%`
    * Binary relational operators: `<`, `>`, `<=`, `>=`, `==`, `!=`
    * Binary bitwise arithmetic operators: `&`, `^`, `|`
    * Conditional operator `?:` (excluding the condition, which is expected to be of type bool)
* Usual arithmetic conversion rules
    * Step 1: if one operand is integral and one if floating, convert to floating. Otherwise, numerically promote integral operands
    * Step 2: After promotion, if one is signed and one is unsigned, special rules (see learncpp arithmetic conversions page). Otherwise, convert operand of lower rank to operand of higher rank
    * Common type: the matching/converted type
    * Ranking (types not listed here would be promoted to the lowest rank, i.e., int):
        * long double (highest rank)
        * double
        * float
        * long long
        * long
        * int (lowest rank)
* Can use `std::common_type` or `std::common_type_t` can be used to find out the common type, eg: `std::common_type<int,doublt>`


## Explicit Type Conversion aka Casting
* Return a temporary object, similar to a return by value
* No compiler warning for unsafe conversions
* Avoid const cast and reinterpret cast - can be harmful if used incorrectly

### C-style cast
* May perform static cast, const cast or reinterpret cast, so should be avoided
```cpp
// convert x to a double so we get floating point division
double d { (double)x / y }; 
double d { double(x) / y };
```

### Static cast
* Most common
* Provides compile-time type checking
* Will prevent dangerous conversions like reinterpretation or discarding constant
* `static_cast<new_type>(expression)`

## References
* [learcpp - Introduction to type conversion and static_cast](https://www.learncpp.com/cpp-tutorial/introduction-to-type-conversion-and-static_cast/)
* [learcpp - implicit type conversion](https://www.learncpp.com/cpp-tutorial/implicit-type-conversion/)
* [cpprefence - Implicit conversion (very detailed and technical)](https://en.cppreference.com/w/cpp/language/implicit_conversion)
* [leancpp - Floating point and integral promotion](https://www.learncpp.com/cpp-tutorial/floating-point-and-integral-promotion/)
* [leancpp - Numeric conversions](https://www.learncpp.com/cpp-tutorial/numeric-conversions/)
* [leancpp - Arithmetic conversions](https://www.learncpp.com/cpp-tutorial/arithmetic-conversions/)
* [cpprefence - Usual arithmetic conversions](https://en.cppreference.com/w/cpp/language/usual_arithmetic_conversions)
* [leancpp - Explicit type conversion casting and static cast](https://www.learncpp.com/cpp-tutorial/explicit-type-conversion-casting-and-static-cast/)
