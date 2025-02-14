# Floating point numbers
* *floating point* - there can be a variable number of digits after the decimal (decimal point can *float*).
* Useful for storing very large or very small numbers, including those with a fractional component
* `float`: almost always 4 bytes
  `double`: almost always 8 bytes
  `long double`: varies depending on the standard used (8 to 16 bytes)

## Literals
```cpp
int a { 5 };      // 5 means integer
double b { 5.0 }; // 5.0 is a floating point literal (no suffix means double type by default)
float c { 5.0f }; // 5.0 is a floating point literal, f suffix means float type

int d { 0 };      // 0 is an integer
double e { 0.0 }; // 0.0 is a double
```

## Precision
* How many significant digits can be stored without loss of information
* Depends on the maximum size of the type and also the value that's being stored
* `float`: 6 to 9
  `double`: 15 to 18
  `long double`: minimum of 15, 18, or 33 depending on how many bytes it occupies
* Information loss occurs if the value to be represented has more significant digits than the precision of that type
* While printing using `std::cout` can set the precision using `std::setprecision(#)`

## Rounding error
* When some significant digits are lost
* Stored in binary. 1/10 stored in binary is an infinite sequence, hence there will be a rounding error. The actual value stored will be slightly higher or lower than 0.1.
* This causes problems when comparing floating point numbers using relational operators if the two operands are close in value (especially while checking `==` and `!=`)
* Check [this page](https://www.learncpp.com/cpp-tutorial/relational-operators-and-floating-point-comparisons/) to see how to write a custom function for checking equality of floating point numbers


# Resources/references
* [learncpp - Floating point numbers](https://www.learncpp.com/cpp-tutorial/floating-point-numbers/)
* [learncpp - Relational operators and floating point comparisons](https://www.learncpp.com/cpp-tutorial/relational-operators-and-floating-point-comparisons/)
