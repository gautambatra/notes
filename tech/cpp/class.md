# Class
## Member functions may be defined in any order
* A member function can call another member function defined and declared later
    * If such a scenario is encountered, the compiler:
        1. Implicitly forward declares the called function
        2. Moves the definition of the member functions to immediately after the class declaration
```cpp
// Original code
struct Foo
{
    int z() { return m_data; } // m_data not declared yet
    int x() { return y(); }    // y not declared yet
    int y() { return 5; }

    int m_data{};
};


// Converted code to be compiled
struct Foo
{
    int z(); // forward declaration of Foo::z()
    int x(); // forward declaration of Foo::x()
    int y(); // forward declaration of Foo::y()

    int m_data{};
};

int Foo::z() { return m_data; } // m_data already declared above
int Foo::x() { return y(); }    // y already declared above
int Foo::y() { return 5; }
```

## Struct vs Class Conventions
* Both can do the same stuff, but by convention, structs are treated as aggregates
* Structs should avoid defining constructors and avoid private/protected members as that makes them non-aggregate


# Why abstraction/data hiding?
* Allows us to maintain class invariants, e.g.:
```cpp
void setName(std::string_view name)
{
    m_name = name;
    m_firstInitial = name.front();
}
```

* Allows for better error detection (e.g., in above code can check for empty string argument and take action)
* Makes it possible to change implementation details without breaking existing programs


## Static Data Members
* Because static member variables are essentially global variables, you must explicitly define (and optionally initialize) the static member outside of the class, in the global scope.
* when the static member is a constant integral type (which includes char and bool) or a const enum, the static member can be initialized inside the class definition
* A static inline variable can be defined and initialized directly (C++17)
* Because constexpr members are implicitly inline (as of C++17), static constexpr members can also be initialized inside the class definition without explicit use of the inline keyword
* Make your static members inline or constexpr so they can be initialized inside the class definition.
* A static member may use auto to deduce its type from the initializer, or Class Template Argument Deduction (CTAD) to deduce template type arguments from the initializer.
```cpp
private:
    auto m_x { 5 };           // auto not allowed for non-static members
    std::pair m_v { 1, 2.3 }; // CTAD not allowed for non-static members

    static inline auto s_x { 5 };           // auto allowed for static members
    static inline std::pair s_v { 1, 2.3 }; // CTAD allowed for static members
```


## Friend Functions
* A function can be a `friend` of multiple classes
* A class member function can be a friend of another class
```cpp
class someClass
{
public:
    void someFunc(SomeType someParam);
    //...
};
void SomeClass::someFunc(SomeType someParam)
{
    //...
}

class anotherClass
{
public:
    // somFunc should be fully defined by this point
    friend void someClass::someFunc(someType someParam);
};
```


## Ref Qualifiers
See [this page](https://www.learncpp.com/cpp-tutorial/ref-qualifiers/)


## Misc
* Mutator = any function that changes the state of the class object, e.g., setters
* Prefer implementing functions as non-members when possible (especially functions that contain application specific data or logic). Helps keep the class interface small, enforces encapsulation.
* Declare public members first, protected members next, and private members last. This spotlights the public interface and de-emphasizes implementation details. Order as per google C++ style guide:
    * Types and type aliases (typedef, using, enum, nested structs and classes, and friend types)
    * Static constants
    * Factory functions
    * Constructors and assignment operators
    * Destructor
    * All other functions (static and non-static member functions, and friend functions)
    * Data members (static and non-static)
* Static member functions have no `this` pointer (not attached to an object)
* Non-aggregate types are not *literal types*, unless they have a constexpr constructor. A literal type is a type for which a `constexpr` object can be created.
* If you want your class to be able to be evaluated at compile-time, make your member functions and constructor constexpr.
