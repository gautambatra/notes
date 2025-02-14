# Unnamed and Inline Namespaces
## Unnamed (anonymous) namespaces
```cpp
namespace // unnamed namespace
{
    void doSomething() // can only be accessed in this file
    {
        std::cout << "v1\n";
    }
}

int main()
{
    doSomething(); // we can call doSomething() without a namespace prefix

    return 0;
}
```
* Content in an unnamed namespace is treated as belonging to its parent namespace (in the above exampe, the global namespace)
* All identifiers in an unnamed namespace have internal linkage, i.e., they are only accessible in the current file
* Can be used to define a bunch of functions only needed in the current file, instead of having to mark each of them as `static`
* Should not be used in header files

## Inline namespaces
* Considered as belonging to the parent namespace (like anonymous namespaces)
* Don't affect linkage (unlike anonymous namespaces)
* Can be used to version content
```cpp
inline namespace V1 // declare an inline namespace named V1
{
    void doSomething()
    {
        std::cout << "V1\n";
    }
}

namespace V2 // declare a normal namespace named V2
{
    void doSomething()
    {
        std::cout << "V2\n";
    }
}

int main()
{
    V1::doSomething(); // calls the V1 version of doSomething()
    V2::doSomething(); // calls the V2 version of doSomething()

    doSomething(); // calls the inline version of doSomething() (which is V1)

    return 0;
}
```

* A namespace can be both inline and unnamed, but it may be better to define the unnamed namespace inside an inline namespace. Names inside that unnamed namespace will still have internal linkage
```cpp
namespace V1 // declare a normal namespace named V1
{
    void doSomething()
    {
        std::cout << "V1\n";
    }
}

inline namespace V2 // declare an inline namespace named V2
{
    namespace // unnamed namespace
    {
        void doSomething() // has internal linkage
        {
            std::cout << "V2\n";
        }

    }
}

int main()
{
    V1::doSomething(); // calls the V1 version of doSomething()
    V2::doSomething(); // calls the V2 version of doSomething()

    doSomething(); // calls the inline version of doSomething() (which is V2)

    return 0;
}
```

## References
* [learcpp - Unnamed and inline namespaces](https://www.learncpp.com/cpp-tutorial/unnamed-and-inline-namespaces/)
