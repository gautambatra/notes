# noexcept
* A function may be *non-throwing* or *pontentially throwing*.
* `noexcept` specifier declares a function as non-throwing.
* Doesn't actually prevent exceptions.
* If an exception is thrown, no stack unwinding occurs and `std::terminate` is called. So even if the exception is handled higher in the call stack, program never reaches it.
* A noexcept function may handle exceptions thrown by it or functions it calls, but it's best to avoid any exceptions altogether.
* Destructors are implicitly non-throwing.
* Following are non-throwing by default for implicity declared or defaulted functions:
    * Constructors
    * Assignments
    * Comparison operators (as of C++20)
* If any of the above implicitly non-throwing function calls a potentially throwing function, it becomes potentially-throwing
* Following are potentially throwing by default:
    * Normal functions
    * User-defined functions
    * User-defined operators

## Why noexcept
* Enables compiler to perform additional optimizations, eg: for noexcept function, compiler need not keep the stack in an unwindable state
* STL containers use the noexcept operator to determine whether to use move semantics (faster) or copy semantics

## noexcept operator
* Returns true/false based on whether the argument will throw an exception
* Can be used to conditionally execute code based on whether something in non-throwing or potentially throwing
```cpp
void foo() {throw -1;}
void boo() {};
void goo() noexcept {};
struct S{};

constexpr bool b1{ noexcept(5 + 3) }; // true; ints are non-throwing
constexpr bool b2{ noexcept(foo()) }; // false; foo() throws an exception
constexpr bool b3{ noexcept(boo()) }; // false; boo() is implicitly noexcept(false)
constexpr bool b4{ noexcept(goo()) }; // true; goo() is explicitly noexcept(true)
constexpr bool b5{ noexcept(S{}) };   // true; a struct's default constructor is noexcept by default
```

## Exception safety gurantantee
* 4 levels:
    * No guarantee
    * Basic guarantee - on an exception, no memory leak will occur but program state may change
    * Strong guarantee - no memory leak and no program state change
    * No-throw / no-fail gurantee - no exception will be thrown

### No-throw gurantee
* An error code will be generated or the exception will be ignored
* Required while unwinding of stack when an exception is already being handled
* Following should be no-throw:
    * Destructors, functions called by destructor
    * Memory cleanup routines, 
    * Functions being called by other no throw functions 

### No-fail guarantee
* Function will never fail (it will always succeed in doing what it intends to)
* Stronger form of no-throw
* Following should be no-fail:
    * Move constructors and move assignments (see notes [[move-semantics#Move constructor and Move Assignment]])
    * Swap functions
    * Clear/erase/reset functions on containers
    * Operations on `std::unique_ptr`
    * Functions called by higher level no-fail functions

## When to use noexcept
* Move constructors
* Move assignment operators
* Swap functions

## References
* [learcpp - exception specifications and noexcept](https://www.learncpp.com/cpp-tutorial/exception-specifications-and-noexcept/)
