# Constructors
* DO NOT create the objects. Compiler sets up the memory allocation prior to the constructor call.
* Perform initialization and setup
* Members are initialised in the order they are defined in the class, not in the order in which they appear in the member initializer list
* Constructors called from a body of another function (including another constructor) will either result in an error or create a temporary object. (constructor chaining, delegating constructors)
    * To call another constructor from a constructor, call it in the member initiliser list, e.g., `Employee(std::string_view name) : Employee{ name, 0 } {}`


## Implicit Conversion
```cpp
void printDouble(double d) // has a double parameter
{
    std::cout << d;
}

int main()
{
    printDouble(5); // we're supplying an int argument
    return 0;
}
```

* Compiler checks if an implicit conversion for int to double is available. 
* The argument `5` is converted to double `5.0`.
* Because the parameter is passed by value, `d` is copy initialised with value `5.0`. 


## User-defined Conversions

```cpp
class Foo
{
private:
    int m_x{};
public:
    Foo(int x)
        : m_x{ x }
    {
    }

    int getX() const { return m_x; }
};

void printFoo(Foo f) // has a Foo parameter
{
    std::cout << f.getX();
}

int main()
{
    printFoo(5); // we're supplying an int argument
    return 0;
}
```
* Parameter and argument are not both fundamental types, so *user-defined conversion* is needed.
* Compiler checks if `int` to `Foo` conversion is available.
* `5` is converted to a `Foo` object using the `Foo(int)` constructor.
* `f` is copy initialised using `Foo(int)` with `5` as argument.
* *Converting Constructor*: a constructor that can perform implicit conversions. 
* Pre C++17: a temporary `Foo` object is created using `Foo(int)` and then `f` is copy constructed using this temporary object.
* Since C++17: Copy constructor is omitted. `f` is copy initialised with value `5`. Works even if copy constructor is deleted.


## Caveat of User-Defined Conversion
```cpp
class Employee
{
private:
    std::string m_name{};

public:
    Employee(std::string_view name)
        : m_name{ name }
    {
    }

    const std::string& getName() const { return m_name; }
};

void printEmployee(Employee e) // has an Employee parameter
{
    std::cout << e.getName();
}

int main()
{
    printEmployee("Joe"); // we're supplying an string literal argument
    return 0;
}
```
* Above program fails compilation because *only one user-defined conversion may be applied*
* Here we have 2 implicit conversions: c-style string literal to string_view and string_view to `Employee` using `Employee(string_view)`
* 2 ways to make this work:
1. Use string_view literal instead of c-style literal:
```cpp
    using namespace std::literals;
    printEmployee( "Joe"sv); // now a std::string_view literal
```

2. Convert to explicit conversion using direct list initialization. See notes [[variable-initialization#Direct Initialization]]
```cpp
printEmployee(Employee{ "Joe" });
```


## Explicit Constructors
* Cannot be used as converting constructor, i.e., no implicit conversion can take place.
* Cannot be used to perform copy initialization or copy list initialization. See notes [[variable-initialization#Copy Initialization]], [[variable-initialization#List Initialization]]
* Can be used for direct initialization and direct list initialization. See notes [[variable-initialization#Direct Initialization]], [[variable-initialization#List Initialization]]

```cpp
class Dollars
{
private:
    int m_dollars{};

public:
    explicit Dollars(int d) // now explicit
        : m_dollars{ d }
    {
    }

    int getDollars() const { return m_dollars; }
};

void print(Dollars d)
{
    std::cout << "$" << d.getDollars();
}

int main()
{
    print(5); // compilation error because Dollars(int) is explicit
    print({5}); // compilation error because Dollars(int) is explicit
    Dollars d1(5); // ok
    Dollars d2{5}; // ok
    Dollars d3 = 5; // compilation error because Dollars(int) is explicit
    Dollars d3 = {5}; // compilation error because Dollars(int) is explicit
    print(Dollars{5}); // ok: explicitly create a Dollars
    print(Dollars(5)); // ok: explicitly create a Dollars
    print(static_cast<Dollars>(5)); // ok: static_cast will use explicit constructors

    return 0;
}
```

### Return by value and explicit constructors
```cpp

class Foo
{
public:
    explicit Foo() // note: explicit (just for sake of example)
    {}

    explicit Foo(int x) // note: explicit
    {}
};

Foo getFoo()
{
    // explicit Foo() cases
    return Foo{ };   // ok
    return { };      // error: can't implicitly convert initializer list to Foo

    // explicit Foo(int) cases
    return 5;        // error: can't implicitly convert int to Foo
    return Foo{ 5 }; // ok
    return { 5 };    // error: can't implicitly convert initializer list to Foo
}
```

### Best practices for explicit
* Make any constructor that will accept a single argument (or single non-optional argument) explicit by default. 
* Copy and move constructors should not be made explicit as they do not perform conversions.
* Default constructors with no arguments should most of the time not be explicit as they are used to convert `{}` to a default value.
* Constructors with multiple arguments need not be made explicit.
* If a conversion is semantically equivalent and performant, can make it non-explicit, eg, c-style string literal to `string_view`. 
    * `string_view` to `string` constructor is explicit because the conversion in non-performant.


## Copy Assignment vs Copy Constructor
* If a new object needs to be created before copying occurs, copy constructor is used.
* If object already exists and is being changed, assignment occurs.


## References
* [learncpp - Converting constructors and the explicit keyword](https://www.learncpp.com/cpp-tutorial/converting-constructors-and-the-explicit-keyword/)
