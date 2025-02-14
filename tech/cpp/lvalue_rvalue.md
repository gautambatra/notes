# Value Categories
* The type of an expression must be determinable at compile time (for proper type checking and type deduction), but the value can be determined at runtime (for non constexprs)
* Value category: indicates whether an expression resolves to a value, a function, or an object.
    * Pre-C++11: only 2 categories: `lvalue` and `rvalue`
    * Since C++11: `glvalue`, `prvalue`, and `xvalue` added to support [move semantics](/tech/cpp/move_semantics.md)

## Lvalue
* aka left value, locator value, l-value.
* An expression that evaluates to an identifiable object, function, or bit-field.
    * Entities with identities can be accessed via an identifier, reference, or pointer, and typically have a lifetime longer than a single expression or statement.
* 2 sub-types:
    * modifiable
    * non-modifiable: const, constexpr

Example:
```cpp
int main()
{
    int x { 5 };
    int y { x }; // x is an lvalue expression

    return 0;
}
```

## Rvalue
* aka right value, r-value
* An expression that is not an lvalue
* Evaluates to a value, eg: literals, (except C-style string literals), function return values, operators that return by value
    * An expression that produces a temporary value is also an rvalue
* Not identifiable, i.e., it only exists within the scope of the expression in which it is used

## Example
```cpp
int return5()
{
    return 5;
}

int main()
{
    int x{ 5 }; // 5 is an rvalue expression
    const double d{ 1.2 }; // 1.2 is an rvalue expression

    int y { x }; // x is a modifiable lvalue expression
    const double e { d }; // d is a non-modifiable lvalue expression
    int z { return5() }; // return5() is an rvalue expression (since the result is returned by value)

    int w { x + 1 }; // x + 1 is an rvalue expression
    int q { static_cast<int>(d) }; // the result of static casting d to an int is an rvalue expression

    return 0;
}
```


## Tips/Insights
* If not sure if an expression is an lvalue or rvalue, try taking it's address (only valid for lvalue)
```cpp
int foo()
{
    return 5;
}

int main()
{
    int x { 5 };
    &x; // compiles: x is an lvalue expression
    &5; // doesn't compile: 5 is an rvalue expression
    &foo(); // doesn't compile: foo() is an rvalue expression
}
```

* A C-style string literal is an lvalue because C-style strings (which are C-style arrays) decay to a pointer. The decay process only works if the array is an lvalue (and thus has an address that can be stored in the pointer). C++ inherited this for backwards compatibility. See notes [[cstylearraydecay]].


## Lvalue to rvalue conversion
* Takes place when an rvalue is expected but lvalue is specified, eg:
```cpp
int x {5};
int y {x}; // x gets converted to rvalue which evaluates to 5
int z;
z = x; // x gets converted to rvalue as assignment RHS must be rvalue
x = x + 1; // here in LHS x is an lvalue, and 'x+1' is an rvalue that evaluates to 6
````
## See Also
[[lvalue-references]]
[[r-value-references]]

## References
* [learcpp - Value categories (lvalues and rvalues)](https://www.learncpp.com/cpp-tutorial/value-categories-lvalues-and-rvalues/)
* [cppreference - Value categories](https://en.cppreference.com/w/cpp/language/value_category)
