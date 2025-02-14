# Enumerations
## Unscoped enumeration
* Enumerators are placed in the scope where the enumeration is defined
*  We can access an unscoped enum value by with or without the enum name prefix, i.e., `Color::BLUE` or `BLUE`
*  if the enum type does not have an explicit base type, compiler may pick any signed or unsigned type depending on the values and the range
* As of C++17, we can brace-initialise unscoped enums using an integral value if the enum has an explicit base type:
```cpp
enum Pet: int // we've specified a base
{
    cat, // assigned 0
    dog, // assigned 1
    pig, // assigned 2
    whale, // assigned 3
};

int main()
{
    Pet pet1 { 2 }; // ok: can brace initialize unscoped enumeration with specified base with integer (C++17)
    Pet pet2 (2);   // compile error: cannot direct initialize with integer
    Pet pet3 = 2;   // compile error: cannot copy initialize with integer

    pet1 = 3;       // compile error: cannot assign with integer

    return 0;
}
```

* Not type safe - implicitly convert to integers
```cpp
int main()
{
    enum Color
    {
        red,
        blue,
    };

    enum Fruit
    {
        banana,
        apple,
    };

    Color color { red };
    Fruit fruit { banana };

    if (color == fruit) // The compiler will compare color and fruit as integers
        std::cout << "color and fruit are equal\n"; // and find they are equal!
    else
        std::cout << "color and fruit are not equal\n";

    return 0;
}
```

## Scoped enumeration
* AKA Enum class
* Do not implicitly convert to integers. Need to explicitly cast. So if you need the conversion might as well use unscoped enum declared within a class or namespace.
* Enumerators are placed in the scope of the enumeration (not the scope where enumeration is defined)
* As of C++17, can be list initialised with an integral value, e.g., `Pet pet { 1 };`
```cpp
int main()
{
    enum class Color // "enum class" defines this as a scoped enumeration rather than an unscoped enumeration
    {
        red, // red is considered part of Color's scope region
        blue,
    };

    enum class Fruit
    {
        banana, // banana is considered part of Fruit's scope region
        apple,
    };

    Color color { Color::red }; // note: red is not directly accessible, we have to use Color::red
    Fruit fruit { Fruit::banana }; // note: banana is not directly accessible, we have to use Fruit::banana

    if (color == fruit) // compile error: the compiler doesn't know how to compare different types Color and Fruit
        std::cout << "color and fruit are equal\n";
    else
        std::cout << "color and fruit are not equal\n";

    return 0;
}
```


## References
* [leancpp - Unscoped enumerator integral conversions](https://www.learncpp.com/cpp-tutorial/unscoped-enumerator-integral-conversions/)
* [leancpp - Scoped enumerations (enum classes)](https://www.learncpp.com/cpp-tutorial/scoped-enumerations-enum-classes/)
