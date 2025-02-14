# Pointers

## Points to remember
* `int* a,b,c;`: only a is a pointer, b and c are normal variables.
  Should be `int* a, * b, * c;` .
* Dereferencing a *wild pointer*, i.e., an uninitialised pointer, leads to undefined behaviour as it has a garbage address.
* Type of a pointer must match the type of the referred object.
* Cannot initialise a pointer with a literal value.
    ```cpp
    int* ptr{ 5 }; // not okay
    int* ptr{ 0x0012FF7C }; // not okay, 0x0012FF7C is treated as an integer literal
    ```
* The address-of operator `&` returns a pointer, not an address in literal form
* Size of an object depends on the architecture: 32 bits (4 bytes) for a 32-bit executable, 8 bytes for a 64-bit executable
* Dangling pointer = pointer holding address of a no longer valid (eg., destroyed) object
* Dereferencing a dangling, wild, null, or invalid pointer leads to undefined behaviour (probably a crash)
    * Any other operation on an invalid pointer has implementation-defined behaviour
* After an object is destroyed, all pointers pointing to it are left dangling, and it's the programmer's responsibility to set them to `nullptr`.
    * Delete will not set the pointer to null. See why [here](https://isocpp.org/wiki/faq/freestore-mgmt#delete-zero)
* A non-const pointer can be pointed to a different object
* A pointer to a non-const value cannot point to a const
* A pointer to const (`const int* ptr`) can change the object it's pointing to. Can point to a non-const value (but can't change it's value)
* A const pointer (`int* const ptr`) cannot be pointed to a different object, but can change the value of the pointed object
* A const pointer to const value (`const int* const ptr`) can neither change the object it's pointing at, nor the value of the object

## Pointers vs references

Pointers | References
---------|-----------
Need to explicitly dereference and get the address | Address-of and dereferencing is automatic
Should be initialised | Must be initialised
Are objects | Are not objects
Can be pointed to a different object | Cannot be reseated
Can point to nothing | Must always be bound to an object
Dangerous | Safe (except dangling references)

## Passing by address
* A pointer is passed (possibly by using address-of)
* Function parameter is a pointer
* The referenced object is not copied, just the pointer i.e, the 4 or 8 byte address of the referenced object is copied. So this is fast, like pass by reference.
* Can modify the actual object by dereferencing the pointer, unless the function parameter is a pointer to const, eg, `func (const int* ptr)`.
* May need null checks to avoid undefined behaviour/crashes.
* Notes on passing by reference: [[lvalue-references#Passing by reference]]


## `nullptr` vs `0` or `NULL`
* `0` can be interpreted as an integer literal or a null pointer literal - may lead to unintended behaviour.
* Definition of `NULL` is not defined by the standard, may be `0`, `0L`, `(void*)0`, or something else.
```cpp
void print(int x) // this function accepts an integer
{
	std::cout << "print(int): " << x << '\n';
}

void print(int* ptr) // this function accepts an integer pointer
{
	std::cout << "print(int*): " << (ptr ? "non-null\n" : "null\n");
}

int main()
{
	int x{ 5 };
	int* ptr{ &x };

	print(ptr);  // always calls print(int*) because ptr has type int* (good)
	print(0);    // always calls print(int) because 0 is an integer literal (hopefully this is what we expected)

	print(NULL); // this statement could do any of the following:
	// call print(int) (Visual Studio does this)
	// call print(int*)
	// result in an ambiguous function call compilation error (gcc and Clang do this)

	print(nullptr); // always calls print(int*)

	return 0;
}
```
* `nullptr` is of type `std::nullptr_t`, defined in `<cstddef>`. This type can only take the value `nullptr`. Eg, `void someFunc(std::nullptr_t)` is a function that can only be called as `someFunc(nullptr)`

## Return by address
* Very similar to [[lvalue-references#Return by reference]].
* Unlike return by reference, allows returning `nullptr`. 
    * Will need null checks.

## Type deduction and pointers
* Type deduction doesn't drop pointers (unlike references, see notes [[lvalue-references#Type deduction and references]]
    ```cpp
    std::string* getPtr(); // some function that returns a pointer

    int main()
    {
        auto ptr1{ getPtr() };  // std::string*
        auto* ptr2{ getPtr() }; // std::string*
    
        return 0;
    }
    ```
* `auto` vs `auto*`
    * For `auto`, deduced type includes the pointer. For `auto*` deduced type doesn't include the pointer, the pointer is applied later.
    * `auto*` expects pointer - will result in compilation error if deduced type is not a pointer
* const pointers:
    * Top-level const is dropped during type deduction (like references)
    * `const auto`, `auto const`: "make the deduced type, i.e., the pointer, const". Results in `Type* const`. Just like `const int` and `int const` are the same thing.
    * `const auto*`: "make the deduced type a pointer to const"
    * `auto* const`: "make the deduced type a const pointer"
    ```cpp
    std::string* getPtr(); // some function that returns a pointer

    int main()
    {
        const auto ptr1{ getPtr() };  // std::string* const 
        auto const ptr2 { getPtr() }; // std::string* const
    
        const auto* ptr3{ getPtr() }; // const std::string*
        auto* const ptr4{ getPtr() }; // std::string* const
    
        std::string s{};
        const std::string* const ptr { &s };

        auto ptr5{ ptr };  // const std::string* (top-level const dropped)
        auto* ptr6{ ptr }; // const std::string* (top-level const dropped)

        auto const ptr7{ ptr };  // const std::string* const (top-level const dropped, then reapplied)
        const auto ptr8{ ptr };  // const std::string* const (top-level const dropped, then reapplied)

        auto* const ptr9{ ptr }; // const std::string* const (top-level const dropped, then reapplied)
        const auto* ptr10{ ptr }; // const std::string* (top-level const dropped, const keyword here has no effect - ptr was already low-level const)

        const auto const ptr11{ ptr };  // error: const qualifer can not be applied twice
        const auto* const ptr12{ ptr }; // const std::string* const
        return 0;
    }
    ```

## Pointers to Pointers
```cpp
int value { 5 };

int* ptr { &value };
std::cout << *ptr << '\n'; // Dereference pointer to int to get int value

int** ptrptr { &ptr };
std::cout << **ptrptr << '\n'; // dereference to get pointer to int, dereference again to get int value

int** ptrptr2 { &&value }; // not valid because & operator expects an lvalue, but &value is an rvalue
```

### Arrays of Ponters
```cpp
int** arr { new int*[10] }; // allocate an array arr of 10 int pointers
                            // Each element is an int pointer
```


## void Pointers
* Can point to any data type
* Dereferencing void pointers is illegal
* Deleting void pointer leads to undefined behaviour, because it doesn't know what it's pointing to.
* Pointer arithmetic cannot be done on void pointer, because it doesn't know ...


## Best practices
* While declaring, place the `*` next to the type.
* Avoid declaring multiple pointers in a single statement.
* Always initialise your pointers
* Value initialize your pointers (to be null pointers) if you are not initializing them with the address of a valid object.
* Use `nullptr` when you need a null pointer literal for initialization, assignment, or passing a null pointer to a function.
    ```cpp
    int x{ 5 };
    int* ptr;        // an uninitialized pointer (holds a garbage address)
    int* ptr2 { nullptr };     // a null pointer 
    int* ptr3 { &x }; // a pointer initialized with the address of variable x
    ```
* If a pointer doesn't point to a valid object (eg., a dangling pointer) set it to `nullptr`. Then all we need is a null check to avoid undefined behaviour/crashes.
* Avoid using `0` and `NULL` instead of `nullptr`
* Favour references over pointers wherever possible
    ```cpp
    
    {
        int x{ 5 };
        ptr = &x; // assign the pointer to an object that will be destroyed (not possible with a reference)
    } // ptr is now dangling and pointing to invalid object

    if (ptr) // condition evaluates to true because ptr is not nullptr
        std::cout << *ptr; // undefined behavior
    ```
* Prefer pass by reference over pass by address wherever possible. Pass by reference allows rvlaues as well.
    * Pass by address allows optional arguments (however this can be achieved by using overloaded functions and pass by reference)

## References
* [learcpp - introduction to pointers](https://www.learncpp.com/cpp-tutorial/introduction-to-pointers/)
* [learcpp - pointers and const](https://www.learncpp.com/cpp-tutorial/pointers-and-const/)
* [learcpp - pass by address](https://www.learncpp.com/cpp-tutorial/pass-by-address/)
* [learcpp - pass by address 2](https://www.learncpp.com/cpp-tutorial/pass-by-address-part-2/)
* [learcpp - return by reference and return by address](https://www.learncpp.com/cpp-tutorial/return-by-reference-and-return-by-address/)
* [learcpp - type deduction with pointers , references, and const](https://www.learncpp.com/cpp-tutorial/type-deduction-with-pointers-references-and-const/)
