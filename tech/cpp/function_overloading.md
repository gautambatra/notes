# Function Overloading
## Function overload differentiation
Following are used for differentiation
* Number of parameters
* Type of parameters
* const or volatile (For member functions)
* Ref qualifiers (for member functions)

Return type is not used.

## Name mangling
Compiler performs name mangling of all functions. Overloaded functions end up getting unique names.

## Resolving function overloads
### Argument matching steps
1. Try to find exact match
2. Try to find match after trivial conversions, including:
    * lvalue to rvalue
    * qualification conversions (e.g., non-const to const)
    * non-reference to reference
   If a match is found at this stage it's still considered an exact match
3. Try to find match after applying numeric promotions
4. Try to find match after applying numeric conversions
5. Try to find match after applying user-defined conversions
5. Try to find match containing ellipsis

In case of multiple arguments, the above steps are followed for each argument and then the best match is chosen if it is unambiguous.


### Ambiguous match
When the compiler finds more than one match at the same step
```cpp
void foo(unsigned int)
{}

void foo(float)
{}

int main()
{
    foo(0);       // int can be numerically converted to unsigned int or to float
    foo(3.14159); // double can be numerically converted to unsigned int or to float

    return 0;
}
```
* Can also be caused by default arguments
* Results in a compile-time error


## Deleting a function
```cpp
void printInt(int x)
{
    std::cout << x << '\n';
}

void printInt(char) = delete; // calls to this function will halt compilation
void printInt(bool) = delete; // calls to this function will halt compilation

int main()
{
    printInt(97);   // okay

    printInt('a');  // compile error: function deleted
    printInt(true); // compile error: function deleted

    printInt(5.0);  // compile error: ambiguous match

    return 0;
}
```

To delete all non-matching overloads:
```cpp
// This function will take precedence for arguments of type int
void printInt(int x)
{
    std::cout << x << '\n';
}

// This function template will take precedence for arguments of other types
// Since this function template is deleted, calls to it will halt compilation
template <typename T>
void printInt(T x) = delete;

int main()
{
    printInt(97);   // okay
    printInt('a');  // compile error
    printInt(true); // compile error

    return 0;
}
```


## References
* [learncpp - Function overload resolution and ambiguous matches](https://www.learncpp.com/cpp-tutorial/function-overload-resolution-and-ambiguous-matches/)
