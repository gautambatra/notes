# `string` and `string_view`
## std::string
* std::getline() does not ignore leading whitespace. If using std::getline() to read strings, use std::cin >> std::ws input manipulator to ignore leading whitespace. This needs to be done for each std::getline() call, as std::ws is not preserved across calls.
```cpp
std::cout << "Pick 1 or 2: ";
int choice{};
std::cin >> choice;

std::cout << "Now enter your name: ";
std::string name{};
std::getline(std::cin >> std::ws, name); // note: added std::ws here

std::cout << "Hello, " << name << ", you picked " << choice << '\n';
```
* Initialising and copying strings is expensive. Do not pass by value.
* Avoid creating short-lived std::string objects.
* Double quoted string literals are C-style strings by default. Add the `s` suffix to make it `std::string`
```cpp
int main()
{
    using namespace std::string_literals; // easy access to the s suffix

    std::cout << "foo\n";   // no suffix is a C-style string literal
    std::cout << "goo\n"s;  // s suffix is a std::string literal

    return 0;
}
```
* `constexpr std::string` has no support till C++17 and limited support in C++20/23. Use `string_view` instead.

## std::string_view
* Provides read-only access/view to an existing string (C-style, std::string, or std::string_view) without making a copy. 
* The following program has only one copy of the string. Declaration of `s` doesn't make a copy, neither does the initialization of `str`. They both provide read-only access.
```cpp
void printSV(std::string_view str) // now a std::string_view
{
    std::cout << str << '\n';
}

int main()
{
    std::string_view s{ "Hello, world!" }; // now a std::string_view
    printSV(s);

    return 0;
}
```
* Prefer `string_view` as function parameters when you only need read-only access. C-style string and std::string can both be implicitly converted to string_view. Reverse is not true.
* Use the `sv` suffix to create string_view literal:
```cpp
using namespace std::string_literals;      // access the s suffix
using namespace std::string_view_literals; // access the sv suffix

std::cout << "foo\n";   // no suffix is a C-style string literal
std::cout << "goo\n"s;  // s suffix is a std::string literal
std::cout << "moo\n"sv; // sv suffix is a std::string_view literal
```
* Has full support for `constexpr`. Prefer this for symbolic constants.
* string_view vs const reference to string as function parameter? See [[lvalue-references#`const std::string&` vs `std::string_view` as function parameters ]]
* may or may not be null-terminated.
* Doesn't own the string. A view is dependent on the object being viewed. If the object being viewed is modified or destroyed while the view is still being used, unexpected or undefined behavior will result:
```cpp
std::string getName()
{
    std::string s { "Alex" };
    return s;
}
int main()
{
    std::string_view sv{};

    { // create a nested block
        std::string s{ "Hello, world!" }; // create a std::string local to this nested block
        sv = s; // sv is now viewing s
    } // s is destroyed here, so sv is now viewing an invalid string

    std::cout << sv << '\n'; // undefined behavior

    std::string_view name { getName() }; // name initialized with return value of function
    std::cout << name << '\n'; // undefined behavior
    return 0;
}
```
* Do not initialize a std::string_view with a std::string literal, as this will leave the std::string_view dangling.
```cpp
std::string_view name { "Alex"s }; // "Alex"s creates a temporary std::string
std::cout << name << '\n'; // undefined behavior
```
* It is okay to initialize a std::string_view with a C-style string literal or a std::string_view literal. It’s also okay to initialize a std::string_view with a C-style string object, a std::string object, or a std::string_view object, as long as that string object outlives the view.

* We get undefined behavior when the underlying string is modified. When the string is modified, the views are *invalidated* and must be revalidated:
```cpp
int main()
{
    std::string s { "Hello, world!" };
    std::string_view sv { s }; // sv is now viewing s

    s = "Hello, universe!";    // modifies s, which invalidates sv (s is still valid)
    std::cout << sv << '\n';   // undefined behavior

    sv = s;                    // revalidate sv: sv is now viewing s again
    std::cout << sv << '\n';   // prints "Hello, universe!"

    return 0;
}
```
* Can return string_view from a function in 2 cases:
1. Returning C-string literals. C-style string literals exist for the entire duration of the program:
```cpp
std::string_view getBoolName(bool b)
{
    if (b)
        return "true";  // return a std::string_view viewing "true"

    return "false"; // return a std::string_view viewing "false"
} // "true" and "false" are not destroyed at the end of the function

```
2. Returning string_view parameters (only if the argument is not a temporary object!):
```cpp
std::string_view firstAlphabetical(std::string_view s1, std::string_view s2)
{
    if (s1 < s2)
        return s1;
    return s2;
}
```

### Modifying the string_view view
* Can use `remove_prefix()` and `remove_suffix()` functions
```cpp
int main()
{
	std::string_view str{ "Peach" };
	std::cout << str << '\n';

	// Remove 1 character from the left side of the view
	str.remove_prefix(1);
	std::cout << str << '\n';

	// Remove 2 characters from the right side of the view
	str.remove_suffix(2);
	std::cout << str << '\n';

	str = "Peach"; // reset the view
	std::cout << str << '\n';

	return 0;
```

## string vs string_view
### Variables
* Use string when:
    * You need a string that you can modify.
    * You need to store user-inputted text.
    * You need to store the return value of a function that returns a std::string.
* Use a std::string_view when:
    * You need read-only access to part or all of a string that already exists elsewhere and will not be modified or destroyed before use of the std::string_view is complete.
    * You need a symbolic constant for a C-style string.
    * You need to continue viewing the return value of a function that returns a C-style string or a non-dangling std::string_view.

### Function parameters
* Use a std::string function parameter when:
    * The function needs to modify the string passed in as an argument without affecting the caller. This is rare.
    * You are using language standard C++14 or older and aren’t comfortable using references yet.
* Use a std::string_view function parameter when:
    * The function needs a read-only string.
    * The function needs to work with non-null-terminated strings.
* Use a const std::string& function parameter when:
    * You are using language standard C++14 or older, and the function needs a read-only string to work with (as std::string_view is not available until C++17).
    * You are calling other functions that require a const std::string, const std::string&, or const C-style string (as std::string_view may not be null-terminated).
* Use a std::string& function parameter when:
    * You are using a std::string as an out-parameter 
    * You are calling other functions that require a std::string&, or non-const C-style string.

### Return types

* Use a std::string return type when:
    * The return value is a std::string local variable or function parameter.
    * The return value is a function call or operator that returns a std::string by value.
* Use a std::string_view return type when:
    * The function returns a C-style string literal or local std::string_view that has been initialized with a C-style string literal.
    * The function returns a std::string_view parameter.
    * Writing an accessor for a std::string_view member.
* Use a std::string& return type when:
    * The function returns a std::string& parameter.
* Use a const std::string& return type when:
    * The function returns a const std::string& parameter.
    * Writing an accessor for a std::string or const std::string member.
    * The function returns a static (local or global) const std::string.

## References
* [learcpp - Introduction to std::string](https://www.learncpp.com/cpp-tutorial/introduction-to-stdstring/)
* [learcpp - Introduction to std::string_view](https://www.learncpp.com/cpp-tutorial/introduction-to-stdstring_view/)
* [learcpp - std::string_view (part 2)](https://www.learncpp.com/cpp-tutorial/stdstring_view-part-2/)
