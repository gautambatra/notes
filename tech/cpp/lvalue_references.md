# Lvalue references

## Introduction
* Reference is an alias to an object
* Any operation applied to a reference applies directly to the object


## Lvalue reference
* aka reference
* Alias for an existing *modifiable* lvalue
* Lvalue reference type examples: `int&`, `double&`, etc.
* Lvalue reference variable: `int& ref {x};`
* Can be used to modify the variable/object being referenced
* All references must be initialised (like constants)
* When initialised, the reference is *bound* to the object via *reference binding*
* Must be bound to a modifiable lvalue:
    ```cpp
    const int y { 5 };
    int& invalidRef { y };  // invalid: can't bind to a non-modifiable lvalue
    int& invalidRef2 { 0 }; // invalid: can't bind to an rvalue const int y { 5 };
    ```
* In most cases, the reference must be of the same type as the referent
    ```cpp
    int x { 5 };
    int& ref { x }; // okay: reference to int is bound to int variable

    double y { 6.0 };
    int& invalidRef { y }; // invalid; reference to int cannot bind to double variable
    double& invalidRef2 { x }; // invalid: reference to double cannot bind to int variable
    ```
* Cannot refer to a `void` type
* Once initialised, cannot be *reseated*, i.e., referenced to a different object:
    ```cpp
    int x { 5 };
    int y { 6 };

    int& ref { x }; // ref is now an alias for x

    ref = y; // assigns 6 (the value of y) to x (the object being referenced by ref)
    // The above line does NOT change ref into a reference to variable y!
    ```
* Follow same scoping and duration as normal variables
* Dangling reference: when an object is destroyed before a reference to it
* **References are not objects**
    * References are not required to exist or occupy storage
    * Compiler may optimise references by replacing with the referent
        * If this is not possible, reference occupies storage
    * Cannot have a reference to reference (lvalue reference must reference an identifiable object). BUT:
        ```cpp
        int var{};
        int& ref1{ var };  // an lvalue reference bound to var
        int& ref2{ ref1 }; // an lvalue reference bound to var
        // this is not a reference to reference as ref1 is an alias of var, an lvalue
        // reference to reference will be of type int&& - an error
        ```
* Under the hood, usually implemented as pointers

## Lvalue reference to const
* aka lvalue reference to const value, reference to const, const reference.
* Can bind to modifiable lvalues, non-modifiable lvalues, and rvalues
* Cannot modify the referent.
    ```cpp
    const int x { 5 };    // x is a non-modifiable lvalue
    const int& ref { x }; // okay: ref is a an lvalue reference to a const value

    std::cout << ref << '\n'; // okay: we can access the const object
    ref = 6;                  // error: we can not modify an object through a const reference
    ```
* If bound to a modifiable lvalue, the object being referenced is treated as a constant and cannot be modified.
* **Can bind to rvalues**
    * A temporary object is created and initialised with the rvalue.
    * The lifetime of the temporary object is extended to match the lifetime of the reference
    ```cpp
    const int& ref { 5 }; // okay: 5 is an rvalue
    // ref binds to that temporary object
    std::cout << ref << '\n'; // prints 5
    ```
    * Allows passing literals to functions that have const reference parameters

* **Can bind to object of different type**, as long as it can be implicitly converted
    * Compiler creates a temporary object of intended type
    ```cpp
    const double& r1 { 5 };  // temporary double initialized with value 5, r1 binds to temporary
    std::cout << r1 << '\n'; // prints 5

    char c { 'a' };
    const int& r2 { c };     // temporary int initialized with value 'a', r2 binds to temporary
    std::cout << r2 << '\n'; // prints 97 (since r2 is a reference to int)
    ```
    * Allows us to pass arguments to function with const references in exactly the same way as pass by value (i.e., with implicit conversion)

## Constexpr lvalue references
* Not used much
* Can only bind to objects with static duration (globals and static locals) because compiler knows where static objects are in memory and can be treated as compile-time constants
    ```cpp
    
    constexpr int& ref1 { g_x }; // ok, can bind to global

    static int s_x { 6 };
    constexpr int& ref2 { s_x }; // ok, can bind to static local

    int x { 6 };
    constexpr int& ref3 { x }; // compile error: can't bind to non-static object
    ```
* For constexpr reference to const object, need both keywords:
    ```cpp
    static const int s_x { 6 }; // a const int
    constexpr const int& ref2 { s_x }; // needs both constexpr and const
    ```

## Passing by reference
* Fundamental types are cheap to copy, but class types (including STL class types) are NOT, and we should avoid making their copies
* Pass by reference avoids making copies
    ```cpp
    void printValue(std::string& y) // parameter type is std::string&
    {
        std::cout << y << '\n';
    } // y is destroyed here
    
    int main()
    {
        std::string x { "Hello, world!" };
        printValue(x); // x is now passed by reference into reference parameter y (inexpensive)
        return 0;
    }
    ```
* Changes made to the parameter affect the argument
* Cannot pass non-modifiable objects by reference
* A function can have a mix of pass by value, pass by reference, and pass by const reference
* Since a reference is implemented as a pointer under the hood, pass by reference is ultimately pass by address (by value). So essentially, everything is indirectly pass by value!

## Passing by const lvalue reference
* Avoids copies (just like pass by reference)
* Guarantees function cannot modify the passed argument, i.e., the object being referenced

## Pass by value vs pass by reference tradeoff
* Cost of copying is high if:
    1. Object is large (takes longer to copy)
    2. There is some additional setup associated with object construction, eg., open a file/database, allocate dynamic memory, etc. Most standard library classes have setup costs.
* Compared to copying, cost of binding a reference to an object is fast, same as copying a fundamental type
* Accessing through a reference is slightly more expensive than accessing through a normal variable. Involves the extra step of determining the object being referenced.
    * Accessing objects passed by value is faster than accessing objects passed by reference

## `const std::string&` vs `std::string_view` as function parameters
* If using c++17 or newer, better to use `string_view` as it can handles wider range of argument types
* Use `const std::string&` if the function needs to call another function that takes a C-style string or `std::string` as arumgent. `string_view` is not null-terminated, and does not efficiently convert to `std::string`
* Coversion of C-style string/literal to `std::string` is expensive, whereas coversion to `std::string_view` is cheap
* Therefore, `const std::string&` only handles `std::string` type arguments efficiently, where `string_view` efficiently handles `const char*`/literal, `std::string` and `std::string_view`
* `const std::string&` parameter has additional step of getting the referenced object, whereas string_view (by value) doesn't

## Return by reference
* Return by value creates a copy of the object being returned - expensive for most class types
* Objects returned by reference must live beyond the scope of the function returning the reference, or a dangling reference will result. Use of this reference leads to undefined behaviour.
* Reference lifetime extension does not work across function boundaries.
  ```cpp
    const int& returnByConstReference1()
    {
        return 5; // returns const reference to temporary object
    }
    const int& returnByConstReference2(const int& ref)
    {
        return ref;
    }
    
    int main()
    {
        const int& ref { returnByConstReference1() };
        std::cout << ref; // undefined behaviour

        // direct binding
        const int& ref1 { 5 }; // extends lifetime
        std::cout << ref1 << '\n'; // okay

        // indirect binding
        const int& ref2 { returnByConstReference2(5) }; // binds to dangling reference
        std::cout << ref2 << '\n'; // undefined behaviour
    
        return 0;
    }
    ```
* Assigning/initialising a normal variable with a returned reference makes a copy
* If a parameter is passed to a function by reference, it's safe to return it by reference
* If an rvalue is passed as const reference, it can be returned as const reference
* The caller can modify values through the returned reference:
    ```cpp
    int& max(int& x, int& y)
    {
        return (x > y) ? x : y;
    }
    
    int main()
    {
        int a{ 5 };
        int b{ 6 };
    
        max(a, b) = 7; // sets the greater of a or b to 7
    
        std::cout << a << b << '\n'; // prints 57
        return 0;
    }
    ```

## Best practices
* While declaring, place the `&` next to the type (although doesn't matter for the compiler)
* Favour lvalue references to const over lvalue references to non-const unless you need to modify the object being referenced.
* Pass fundamental types, enums, and string_view by value
* Favour passing by const reference unless the argument needs to be modified.
* Favour pass by (const) reference for types that are expensive to copy
* If not sure if a type is cheap/expensive to copy, favour pass by (const) reference
* Prefer passing strings using std::string_view (by value) instead of const std::string&, unless your function calls other functions that require C-style strings or std::string parameters.
* Never return a (non-static) local variable or temporary by reference.
* Avoid returning references to non-const local static variables. All callers will end up with with reference to the same variable:
    ```cpp
    const int& getNextId()
    {
        static int s_x{ 0 }; // note: variable is non-const
        ++s_x; // generate the next id
        return s_x; // and return a reference to it
    }
    
    int main()
    {
        const int& id1 { getNextId() }; // id1 is a reference
        const int& id2 { getNextId() }; // id2 is a reference
    
        std::cout << id1 << id2 << '\n'; // prints 22
    
        return 0;
    }
    ```
* Use pass by non-const reference for in-out parameters (the passed object is being changed) 
* Use pass by reference instead of returning an expensive-to-copy object by value

## Type deduction and references
* Type deduction drops reference by default
* Use `auto&` to reapply the reference
* Type deduction drops top-level const qualifiers (i.e., const qualifier that applies to the current object, not the referenced object)
    ```cpp
    const int x;    // this const applies to x, so it is top-level
    int* const ptr; // this const applies to ptr, so it is top-level
    const int& ref; // this const applies to the object being referenced, so it is low-level
    const int* ptr; // this const applies to the object being pointed to, so it is low-level
    ```
    * If the initializer is a reference to const (or constexpr), the reference is dropped first (and then reapplied if applicable), and then any top-level const is dropped from the result.
        ```cpp
        const std::string& getConstRef(); // some function that returns a reference to const

        int main()
        {
            auto ref1{ getConstRef() }; // std::string (reference dropped, then top-level const dropped from result)
            const auto ref2{ getConstRef() };  // const std::string (reference dropped, const reapplied)

            auto& ref3{ getConstRef() };       // const std::string& (reference reapplied, low-level const not dropped)
            const auto& ref4{ getConstRef() }; // const std::string& (reference and const reapplied)
        
            return 0;
        }
        ```
        * Dropping a reference may change a low-level const to a top-level const: `const std::string&` is a low-level const, but dropping the reference yields `const std::string`, which is a top-level const.
        * constexpr references work the same way as const (reference dropped, then top-level constexpr)

## References
* [learcpp - lvalue references](https://www.learncpp.com/cpp-tutorial/lvalue-references/)
* [learcpp - lvalue references to const](https://www.learncpp.com/cpp-tutorial/lvalue-references-to-const/)
* [learcpp - pass by lvalue references](https://www.learncpp.com/cpp-tutorial/pass-by-lvalue-reference/)
* [learcpp - pass by const lvalue references](https://www.learncpp.com/cpp-tutorial/pass-by-const-lvalue-reference/)
* [learcpp - return by reference and return by address](https://www.learncpp.com/cpp-tutorial/return-by-reference-and-return-by-address/)
* [stackoverflow - Why is it illegal to take the address of an rvalue temporary?](https://stackoverflow.com/questions/8763398/why-is-it-illegal-to-take-the-address-of-an-rvalue-temporary/9779765#9779765)
* [learcpp - type deduction with pointers , references, and const](https://www.learncpp.com/cpp-tutorial/type-deduction-with-pointers-references-and-const/)
