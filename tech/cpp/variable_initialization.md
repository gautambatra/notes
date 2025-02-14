# Variable Initialization
## Default Initialization
```cpp
int a;
```
* In most cases value is undefined

## Copy Initialization
```cpp
int a = 5;
```
* Inherited from C
* Had fallen out of favour due to being less efficient, but C++17 fixed a lot of issues, now, it ain't so bad.
* Also used during implicit conversions and implicit copying, eg.,
    * passing parameters to function by value
    * returning from function by value
    * catching exceptions by value

## Direct Initialization
```cpp
int a(5);
```
* Introduced for better efficiency while initialising complex objects
* Superseded by list initialization for most cases (still preferred for some scenarios)
* Hard to differentiate variables from functions:
  ```cpp
  int x();  // forward declaration of function x
  int x(0); // definition of variable x with initializer 0
  ```
## List Initialization
* Modern way to initialise
* AKA uniform initialization or brace initialization
* 3 forms:
    1. direct list initialization
    1. copy list initialization
    1. value initialization
  ```cpp
  int width { 5 };    // direct list initialization of initial value 5 into variable width
  int height = { 6 }; // copy list initialization of initial value 6 into variable height
  int depth {};       // value initialization 
  ```
* Advantage: narrowing conversions are error scenarios (more type safe, lesser implicit conversions):
  ```cpp
  int width { 4.5 }; // error: a number with a fractional value can't fit into an int
  ```
  Conversions that don't result in data loss are allowed
* Direct list initialization accepts explicit constructors (See notes [[constructors#Explicit Constructors]])
* Allows initialization of containers as well, eg:
  ```cpp
  std::vector<int> primes{ 2, 3, 5, 7 };          // vector containing 4 int elements with values 2, 3, 5, and 7
  std::vector vowels { 'a', 'e', 'i', 'o', 'u' }; // Uses CTAD (C++17) to deduce element type char (preferred).
  ```
    * Uses the list constructor - allows construction of the container using an initializer list. The constructor does the following:
        * Ensures the container has enough storage to hold all the initialization values
        * Sets the length of the container to the number of elements in the initializer list
        * Initializes the elements to the values in the initializer list in sequential order.
    * CTAD = class template argument deduction. See notes [[ctad]]
    * List constructor is preferred over others:
      ```cpp
      // Copy init
      std::vector<int> v1 = 10;     // 10 not an initializer list, copy init won't match explicit constructor: compilation error

      // Direct init
      std::vector<int> v2(10);      // 10 not an initializer list, matches explicit single-argument constructor

      // List init
      std::vector<int> v3{ 10 };    // { 10 } interpreted as initializer list, matches list constructor

      // Copy list init
      std::vector<int> v4 = { 10 }; // { 10 } interpreted as initializer list, matches list constructor
      std::vector<int> v5({ 10 });  // { 10 } interpreted as initializer list, matches list constructor
      ```


### Value Initialization
```cpp
int a {};
```
* Initialises the variable to zero or empty (whichever is more appropriate)
* Use value initialization if you are going to update anyway, otherwise use explicit initialization, eg. `int a{0}`

# Other Info
* Use `[[maybe_unused]]` to avoid unused variable warnings (occurs if a variable is initialised but never used)

## Best Practices
* Use a mix of copy, list and direct initialization depending upon the situation.
* Always initialise variables. Uninitialised variables may lead to undefined behaviour.
* When constructing a container (or any type that has a list constructor) with initializers that are not element values, use direct initialization.
* Use copy initialization when using `auto` as list/uniform initialization has some ambiguities (menioned in the isocpp page linked below)

## References
* [learncpp tutorial](https://www.learncpp.com/cpp-tutorial/variable-assignment-and-initialization/)
* [isocpp: prefer the {}-initializer syntax](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#Res-list)
* [learcpp - uninitialized variables and undefined behavior](https://www.learncpp.com/cpp-tutorial/uninitialized-variables-and-undefined-behavior/)
* [stackoverflow - direct vs copy initialization](https://stackoverflow.com/questions/1051379/is-there-a-difference-between-copy-initialization-and-direct-initialization)
