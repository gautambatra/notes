# Program Startup
## Initialization
### Static Initialization
* 2 phases:
** Global variables with `constexpr` Initialization
** Global variables without initialisers are zero initialised (a form of static initialization with constexpr val = 0)

### Dynamic Initialization
* Global variables with non-constextpr initialisers

* Within a file happens in order of definition.
* The order of initialization of static objects across different translational units is ambiguous
    * [Static Initialization Order Fiasco](https://en.cppreference.com/w/cpp/language/siof)
        - Results may depend on when compiler initialises, and compilation order of files

### Best Practices
* Avoid globals, prefer locals
* Always make globals `static` or `const` (should only be accessible in the file it's declared), and provide access functions (to make it implementation agnostic)
* Avoid initialising static variables using static variables from different translational unit
* Avoid dynamic initialization of global variables
* Put globals in a namespace, or at least prefix them (eg: `g_varName`)
* Don't use globals directly in functions, pass them as arguments (modular)
