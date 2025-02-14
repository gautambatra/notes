# Function templates
## Function template instantiation
*  A function template is only instantiated the first time a function call is made in each translation unit. Further calls to the function are routed to the already instantiated function.
* If no function call is made to a function template, the function template won’t be instantiated in that translation unit.
* Undesired instantiations can be marked as delete:
```cpp
template <>
const char* addOne(const char* x) = delete;
```
* Each instantiation will get it's own copy of static local variables
* Templates that are needed in multiple files should be defined in a header file, and then #included wherever needed. This allows the compiler to see the full template definition and instantiate the template when needed. Note: templates are exempt from the one definition rule (ODR). Also, the instantiations are implicitly inline, so they're also exempt.


## Argument deduction
* If the type of the argument matches the actual type desired, we need not specify the actual type
Thus, for the below template function, instead of `max<int>(1,2)`, we can write `max<>(1,2)` or `max(1,2)`, since `1` and `2` are `int`. If the empty angled brackets are present, only templatised overloads will be considered, otherwise (without the angled brackets), even non-template overloads of `max` will be considered.
```cpp
template <typename T>
T max(T x, T y)
{
    std::cout << "called max<int>(int, int)\n";
    return (x < y) ? y : x;
}

int max(int x, int y)
{
    std::cout << "called max(int, int)\n";
    return (x < y) ? y : x;
}

int main()
{
    std::cout << max<int>(1, 2) << '\n'; // calls max<int>(int, int)
    std::cout << max<>(1, 2) << '\n';    // deduces max<int>(int, int) (non-template functions not considered)
    std::cout << max(1, 2) << '\n';      // calls max(int, int)

    return 0;
}
```
* Type conversion is not performed during instantiation. It's only during resolving overloads. So if a `max(T x, T y)` template exists, the call `max (1, 2.3)` will result in a compilation error, and not a `max(double x, double y)` instantiation. Can `static_cast<double>` the `2` to force this.
To achieve this via a template function, need different type parameters, and an `auto` return type, because the return type may be either of the two parameters:
```cpp
template <typename T, typename U>
auto max(T x, U y) // ask compiler can figure out what the relevant return type is
{
    return (x < y) ? y : x;
}

int main()
{
    std::cout << max(2, 3.5) << '\n';

    return 0;
}
```


## Non-type template parameters
Template parameter with a fixed type (can be `auto`). Serves as a placeholder for a constexpr value.
```cpp
template <int N> // declare a non-type template parameter of type int named N
void print()
{
    std::cout << N << '\n'; // use value of N here
}

int main()
{
    print<5>(); // 5 is our non-type template argument

    return 0;
}
```


## Drawbacks
* May lead to code bloat if a large number of instantiations are created
* Produce almost unreadable error messages


## References
[learncpp - Function template instantiation](https://www.learncpp.com/cpp-tutorial/function-template-instantiation/)
[learncpp - Function templates with multiple template types](https://www.learncpp.com/cpp-tutorial/function-templates-with-multiple-template-types/)
