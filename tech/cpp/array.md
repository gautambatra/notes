# std::array
* Non-constants and runtime constants cannot be used as length:
```cpp
std::array<int, 5> a {};  // a std::array of 5 ints

constexpr int len { 8 };
std::array<int, len> b {}; // Length must be constexpr

enum Colors
{
    red,
    green,
    blue,
    max_colors
};
std::array<int, max_colors> c {}; // Using an enumerator

#define DAYS_PER_WEEK 7
std::array<int, DAYS_PER_WEEK> d {}; // Using a macro (don't do this, use a constexpr variable instead)
```

* std::array is an aggregate, so it has no constructors, only aggregate initialization:
```cpp
std::array<int, 6> fibonnaci = { 0, 1, 1, 2, 3, 5 }; // copy-list initialization using braced list
std::array<int, 5> prime { 2, 3, 5, 7, 11 };         // list initialization using braced list (preferred)

std::array<int, 5> a;   // Members default initialized (int elements are left uninitialized)
std::array<int, 5> b{}; // Members value initialized (int elements are zero initialized) (preferred)

std::vector<int> v(5);  // Members value initialized (int elements are zero initialized) (for comparison)

std::array<int, 4> b { 1, 2 };          // b[2] and b[3] are value initialized
```

* Has full const and constexpr support (vector doesn't)

* C++17 onwards, we have CTAD. Partial omission of template arguments is not supported.
```cpp
constexpr std::array a1 { 9, 7, 5, 3, 1 }; // The type is deduced to std::array<int, 5>
constexpr std::array a2 { 9.7, 7.31 };     // The type is deduced to std::array<double, 2>
constexpr std::array<int> a2 { 9, 7, 5, 3, 1 };     // error: too few template arguments (length missing)
constexpr std::array<5> a2 { 9, 7, 5, 3, 1 };       // error: too few template arguments (type missing)

```

* Elements can be accessed using `[]`, out of range indexes lead to undefined behaviour.
* std::array::size_type is always an alias to std::size_t

## Indexing
* `at()` does runtime bound checking and throws exception for out of bounds access, but should be avoided since we need compile-time bound checking or checking before indexing
* `std::get<>` does compile-time bound checking for conxtexpr indices
```cpp
constexpr std::array prime{ 2, 3, 5, 7, 11 };
std::cout << std::get<3>(prime); // print the value of element with index 3
std::cout << std::get<9>(prime); // invalid index (compile error)
```


## Passing to functions

# C-style array
* Can be assigned to a pointer directly (without taking address)
```cpp
int myarr[40];
int* myPtr = myarr;
int arrSize = sizeof(myarr); // 40 * sizeof(int)
int ptrSize = sizeof(myPtr); // 8 on a 64 bit machine
```
