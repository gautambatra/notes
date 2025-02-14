# Type alias
## using
* `using Distance = double; // define Distance as an alias for type double`
* Compiler replaces the alias with the actual type
* Doesn't define a new type, just an identifier
* Different aliases to the same type are syntactically identical
* Recommended naming convention: start with capital letter and avoid any suffixes
* Follow same scoping rules as normal identifiers
* May be defined in header files
* Can be templatised

## typedef
* Old style for defining type alias
* `typedef double Distance; // aliased type name first`
* Ugly syntax for complex types:
```cpp
typedef int (*FcnType)(double, char); // FcnType hard to find
using FcnType = int(*)(double, char); // FcnType easier to find
```


## References
* [learncpp - Typedefs and type aliases](https://www.learncpp.com/cpp-tutorial/typedefs-and-type-aliases/)
