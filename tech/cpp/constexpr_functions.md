# constexpr functions
* can be used in a constant expression
* Can be evaluated at compile time or runtime (depending on the arguments), so that duplication is not required
* For compile-time evaluation:
    * Arguments must be known at compile-time
    * All statements in the function must be evaluatable at compile-time
```cpp
constexpr int greater(int x, int y)
{
    return (x > y ? x : y);
}

int main()
{
    int x{ 5 }; // not constexpr
    int y{ 6 }; // not constexpr
    constexpr int a{ 5 }; 
    constexpr int b{ 6 };

    std::cout << greater(x, y) << " is greater!\n"; // will be evaluated at runtime
    std::cout << greater(a, b) << " is greater!\n"; // will be evaluated at compile-time

    return 0;
}
```
* Compile-time evaluation is only guaranteed when a constant expression is required
```cpp

constexpr int getValue(int x);

int x { getValue(5) }; // may evaluate at runtime or compile-time (x is not a constant expression)
```
* Implicitly inline (exempt from ODR). Define in header file if needed in multiple files. 


## Compile-time evaluation scenarios
### Always evaluated at compile-time
* Constexpr function is called where constant expression is required.
* Constexpr function is called from other function being evaluated at compile-time.

### Probably evaluated at compile-time
* Constexpr function is called where constant expression isn’t required, all arguments are constant expressions.

### Possibly evaluated at compile-time
* Constexpr function is called where constant expression isn’t required, some arguments are not constant expressions but their values are known at compile-time.
* Non-constexpr function capable of being evaluated at compile-time, all arguments are constant expressions.

### Never evaluated at compile-time
* Constexpr function is called where constant expression isn’t required, some arguments have values that are not known at compile-time.


## constexpr Member Functions
* If you want your class to be able to be evaluated at compile-time, make your member functions and constructor constexpr.
* As of C++14, constexpr member functions are no longer implicitly const.


## References
* [learncpp - constexpr functions](https://www.learncpp.com/cpp-tutorial/constexpr-functions/)
* [learncpp - constexpr functions (part 2)](https://www.learncpp.com/cpp-tutorial/constexpr-functions-part-2/)
