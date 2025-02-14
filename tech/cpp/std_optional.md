# std::optional
```cpp
#include <optional> // for std::optional (C++17)

// Our function now optionally returns an int value
std::optional<int> doIntDivision(int x, int y)
{
    if (y == 0)
        return {}; // or return std::nullopt
    return x / y;
}

int main()
{
    std::optional<int> result1 { doIntDivision(20, 5) };
    if (result1) // if the function returned a value
        std::cout << "Result 1: " << *result1 << '\n'; // get the value
    else
        std::cout << "Result 1: failed\n";

    std::optional<int> result2 { doIntDivision(5, 0) };

    if (result2)
        std::cout << "Result 2: " << *result2 << '\n';
    else
        std::cout << "Result 2: failed\n";

    return 0;
}
```
* Introduced in C++17
* Similarities with pointers:
    * Can hold a value or std::nullopt .
    * Can dereference to get value .
    * Dereferencing nullopt leads to undefined behaviour.
* Differences from pointers:
    * Pointers have reference semantics. They reference another object. Assignment/return by reference copies the pointer, not the object.
    * `std::optional` has value semantics. They hold actual value. Assignment/return by reference copies the actual value.


## Initialization
```cpp
std::optional<int> o1 { 5 };            // initialize with a value
std::optional<int> o2 {};               // initialize with no value
std::optional<int> o3 { std::nullopt }; // initialize with no value
```

## Checking for Value
```cpp
if (o1.has_value()) // call has_value() to check if o1 has a value
if (o2)             // use implicit conversion to bool to check if o2 has a value
```

## Getting the Value
```cpp
std::cout << *o1;             // dereference to get value stored in o1 (undefined behavior if o1 does not have a value)
std::cout << o2.value();      // call value() to get value stored in o2 (throws std::bad_optional_access exception if o2 does not have a value)
std::cout << o3.value_or(42); // call value_or() to get value stored in o3 (or value `42` if o3 doesn't have a value)
```

## Advantages
* Readability - documents that function may return value or not.
* No need to remember error codes/sentinel values for error conditions.

## Disadvantages
* Must check for null before dereferencing.
* No provision for holding reason for failure.

## Usage as Optional Function Parameter
```cpp
void printIDNumber(std::optional<const int> id = std::nullopt)
{
    if (id)
        std::cout << "Your ID number is " << *id << ".\n";
    else
        std::cout << "Your ID number is not known.\n";
}

int main()
{
    printIDNumber(); // we don't know the user's ID yet

    int userid { 34 };
    printIDNumber(userid); // we know the user's ID now

    printIDNumber(62); // we can also pass an rvalue

    return 0;
}
```

* Allows passing of rvalue expressions to optional parameters
* Effectively documents that the parameter is optional
* Doesn't support references, so is always passed by value, hence should be avoided for expensive-to-copy types. In that case use `const T* arg = nullptr` instead of `std::optional<T>`.

## Best Practice
* Use `std::optional` for optional return types, i.e., when a function can fail, but we don't care about the reason for failure.
* Prefer function overloading for optional function parameters (when possible). Otherwise, use std::optional<T> for optional arguments when T would normally be passed by value. Favor const T* when T is expensive to copy.


## References
* [learncpp - std::optional](https://www.learncpp.com/cpp-tutorial/stdoptional/)

