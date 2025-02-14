# Vector
* Elements are stored contiguously
* Can access undelying contiguous storage using `data()` member function which returns a pointer to the first element (like a C-style array)
* Indexing using `at()` does runtime bound checking and throws exception for out of bounds access, but should be avoided since we need compile-time bound checking or checking before indexing


## Capacity and Size
* `push_back()` and `emplace_back()` cause reallocation of the vector if capacity is not sufficient
* How much the capacity is increased is up to the compiler:
    * GCC and Clang double the capacity
    * Visual studio 2022 multiplies current capacity by 1.5
* `resize()` sets both capacity and length
  ```cpp
  std::vector<int> myvec (3); // changes capacity and size to 3
  ```
    * Should only be used if we plan to access/modify elements using subscripts
    * Calling `push_back()` after above line will change size to 4 and capacity to 6 (in gcc/clang)
* `reserve()` modifies capacity but not size

## Reallocation is expensive
* Resize, push_back and emplace_back will all cause reallocation if capacity is not sufficient
* Steps:
    * Acquire new memory with desired capacity
    * Copy (or move if possible) existing elements to the new memory
    * Return old memory to the system
    * Update capacity and length (size)
* Reallocation/resizing is expensive, be watchful, use reserve wherever possible

## `push_back()` vs `emplace_back()`
```cpp
class Foo
{
private:
    std::string m_a{};
    int m_b{};

public:
    Foo(std::string_view a, int b)
        : m_a { a }, m_b { b }
        {}

    explicit Foo(int b)
        : m_a {}, m_b { b }
        {};
};
int main()
{
    std::vector<Foo> stack{};

	// When we already have an object, push_back and emplace_back are similar in efficiency
	Foo f{ "a", 2 };
	stack.push_back(f);    // prefer this one
	stack.emplace_back(f);

	// When we need to create a temporary object to push, emplace_back is more efficient
	stack.push_back({ "a", 2 }); // creates a temporary object, and then copies it into the vector
	stack.emplace_back("a", 2);  // forwards the arguments so the object can be created directly in the vector (no copy made)

	// push_back won't use explicit constructors, emplace_back will
	stack.push_back({ 2 }); // compile error: Foo(int) is explicit
	stack.emplace_back(2);  // ok
}
```
* If object to be appended already exists, they are identical, prefer `push_back()`
* If we are creating a temporary object to be appended, `emplace_back()` can be more efficient
    * we don’t need to create a temporary object to pass
    * we pass the arguments that would be used to create the temporary object, and `emplace_back()` forwards them (using a feature called perfect forwarding) to the vector, where they are used to create and initialize the object inside the vector.
    * avoids a copy that would have otherwise been made.
*  `push_back()` doesn’t allow explicit constructors, whereas `emplace_back()` does. This makes emplace_back more dangerous, as it is easier to accidentally invoke an explicit constructor to perform some conversion that doesn’t make sense.
    * So `push_back()` is safer, and `emplace_back()` can be faster
      ```cpp
      vec1.push_back(1<<20);
      vec2.emplace_back(1<<20); 
      ```
      if vec1 and vec2 are vector<vector<int>>, `push_back()` gives compilation error, whereas `emplace_back()` constructs a vector of 1<<20 size (1048576) 
* If you have a choice between `push_back()` and `emplace_back()` with the same arguments, your code will tend to be more readable if you choose `push_back()`, because `push_back()` expresses your intent more specifically. 


## Avoid std::vector<bool>
* Has memory overhead
* Performance is implementation dependent as optimizations are optional. Without optimizations performance is bad.
* Not a proper STL vector, may lead to compatibility issues. 
* Prefer `std::bitset` if size is fixed
* Prefer `std::vector<char>` for resizable alternative as it is a proper STL vector. Another option: `boost::dynamic_bitset`

## References
* [learcpp - std::vector and stack behavior](https://www.learncpp.com/cpp-tutorial/stdvector-and-stack-behavior/)
* [Abseil C++ Tip: emplace vs. push_back](https://abseil.io/tips/112)
* [learcpp - std::vector<bool>](https://www.learncpp.com/cpp-tutorial/stdvector-bool/)
