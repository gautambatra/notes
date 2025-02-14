# Using
## Using-declaration
```cpp
int main()
{
   using std::cout; // this using declaration tells the compiler that cout should resolve to std::cout
   cout << "Hello world!\n"; // so no std:: prefix is needed here!

   return 0;
} // the using declaration expires at the end of the current scope
```
* Avoid using-declarations in header files.

## Using-directive
```cpp

int main()
{
   using namespace std; // all names from std namespace now accessible without qualification
   cout << "Hello world!\n"; // so no std:: prefix is needed here

   return 0;
} // the using-directive ends at the end of the current scope
```
* Downsides:
    * Allow unqualified access to *all* names from that namespace
    * Do not give priority to the names from the namespace over other names
    * Makes code vulnerable to future name clashes (namespace may be updated - may introduce better matching functions too)


## References
* [learcpp - Using-declarations and using-directives](https://www.learncpp.com/cpp-tutorial/using-declarations-and-using-directives/)
