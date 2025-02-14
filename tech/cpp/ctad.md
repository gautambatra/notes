# CTAD
## Introduction
* CTAD = Class Template Argument Deduction
* Introduced in C++17
* Must have no template arguments at all
```cpp
std::pair<int, int> p1{ 1, 2 }; // explicitly specify class template std::pair<int, int> (C++11 onward)
std::pair p2{ 1, 2 };           // CTAD used to deduce std::pair<int, int> from the initializers (C++17)
std::pair<> p1 { 1, 2 };    // error: too few template arguments, both arguments not deduced
std::pair<int> p2 { 3, 4 }; // error: too few template arguments, second argument not deduced
std::pair p1 { 3.4f, 5.6f }; // deduced to pair<float, float>
std::pair p2 { 1u, 2u };     // deduced to pair<unsigned int, unsigned int>
```


## Argument Deduction Guides
* Following fails in C++17:
```cpp
template <typename T, typename U>
struct Pair
{
    T first{};
    U second{};
};

Pair<int, int> p1{ 1, 2 }; // ok: we're explicitly specifying the template arguments
Pair p2{ 1, 2 };           // compile error in C++17 (okay in C++20)
```
* Compiler doesn't know how to deduce template arguments for agregate class templates
* Solution: provide *deduction guide*:
```cpp
template <typename T, typename U>
struct Pair
{
    T first{};
    U second{};
};

// Here's a deduction guide for our Pair (needed in C++17 only)
// Pair objects initialized with arguments of type T and U should deduce to Pair<T, U>
template <typename T, typename U>
Pair(T, U) -> Pair<T, U>;

Pair<int, int> p1{ 1, 2 }; // explicitly specify class template Pair<int, int> (C++11 onward)
Pair p2{ 1, 2 };           // CTAD used to deduce Pair<int, int> from the initializers (C++17)
```

* Doesn't work for non-static member initialization:
```cpp
struct Foo
{
    std::pair<int, int> p1{ 1, 2 }; // ok, template arguments explicitly specified
    std::pair p2{ 1, 2 };           // compile error, CTAD can't be used in this context
};
```

* Doesn't work for function parameters (it's template *argument* deduction, not parameter deduction)
```cpp
void print(std::pair p) // compile error, CTAD can't be used here
{
    std::cout << p.first << ' ' << p.second << '\n';
}
std::pair p { 1, 2 }; // p deduced to std::pair<int, int>
print(p);
```


## Type Template Parameters with Default Values
```cpp
template <typename T=int, typename U=int> // default T and U to type int
struct Pair
{
    T first{};
    U second{};
};

template <typename T, typename U>
Pair(T, U) -> Pair<T, U>;

Pair<int, int> p1{ 1, 2 }; // explicitly specify class template Pair<int, int> (C++11 onward)
Pair p2{ 1, 2 };           // CTAD used to deduce Pair<int, int> from the initializers (C++17)
Pair p3;                   // uses default Pair<int, int>
```


## References
* [learncpp - ctad](https://www.learncpp.com/cpp-tutorial/class-template-argument-deduction-ctad-and-deduction-guides/)
