# Dynamic Memory Allocation

## Example
```cpp
int *ptr{new int}; // ptr holds the address of the newly allocated memory
*ptr = 7; // dereference the pointer to access the memory and assign a value to it
int *ptr1{new int(5)}; // direct initialization
int *ptr2{new int{6}}; // uniform initialization

delete ptr1; // return memory pointed to by ptr1 back to OS
ptr1 = nullptr; // set ptr1 to null
```

## When to use dynamic allocation?
* When we don't know the size of a variable/array at compile time
* When we need large amounts of memory (stack memory is generally small - few MB's)

## Heap vs Stack
* Accessing heap-allocated objects is slower than stack
    * Compiler knows address of stack objects - can directly access
    * For heap, 2 steps: get address of object from the pointer, then dereference to get value
* Additional complexity for heap: the allocated memory needs to be a contiguous chunk:
     * While allocation, the heap area must be scanned to find a continguous chunk big enough to meed the request
     * While deallocation, the deallacated chunk must be coalesced with any adjacent free chunks to handle future requests efficiently

## How does dynamic allocation work?
* Program requests OS for memory
* If available, OS reserves the memory and returns the address of the memory 
* Program is responsible for for requesting and disposing the memory from the heap area
* When returned to the OS, the memory may be used by another program
* Sequential memory requests may not result in sequential memory addresses being allocated

## Deleting Memory
* 2 steps:
    * Destructor is called
    * Memory is freed
* The memory is returned back to the OS, and can be reassigned to something else (within the same application or to another application)
* Delete will not set the pointer to null. See why [here](https://isocpp.org/wiki/faq/freestore-mgmt#delete-zero)
* Deleting a pointer that is not pointing to dynamically allocated memory (or has already been deleted) is a "serious error" (according to isocpp) and leads to undefined behaviour

## Dangling Pointers
* A pointer pointing to memory that has been deallocated is known as a *dangling pointer*
* A deleted pointer may still be pointing to the memory that has been deallocated.
* Dereferencing or deleting a dangling pointer leads to undefined behaviour
* Note: *deleting a null pointer does nothing. It's perfectly safe to call delete on a nullptr, therefore, it's not recommended to check the pointer before deleting*:
  ```cpp
  // instead of this:
  if (ptr)
      delete ptr;

  // do this:
  delete ptr; // if ptr is null, nothing will happen
  ```
* Best practices
    * If size of object is known at compile time and it's not too big, use stack
    * Avoid new and delete in favour of smart pointers wherever possible
        * Use `make_unique` and `make_shared` instead of `new` wherever possible (avoids need to `delete` as well)
    * Avoid multiple pointers to the same memory. If this is not possible, be clear about who owns the memory and is responsible for deleting it.
    * Set pointers to nullptr after deleting them if they're not going out of scope immediately after deletion.
    * Do not do null check before deleting, language guarantees delete on nullptr does nothing

## Exception on Allocation
* `new` throws `bad_alloc` exception on failure - so no null check is required (this is true for most modern compilers)
* Can tell new to not throw:
  ```cpp
  int* value { new (std::nothrow) int }; // value will be set to a null pointer if the integer allocation fails
  if (!value) // handle case where new returned null
  {
    // Do error handling here
    std::cerr << "Could not allocate memory\n";
  }
  ```

## Memory Leaks
* Happen if a memory is allocated but never deallocated. In such cases the memory is only freed when the program terminates.
* Can happen due to:
    * pointer going out of scope without `delete` being called (hence no references are left to the memory it was pointing to)
      ```cpp
      void someFunc()
      {
          int *ptr{new int{5}};
          // do stuff with ptr

          // no delete called in someFunc
      }
      ```
    * pointer being reassigned before deleting old memory
      ```cpp
      int val = 6;
      int *ptr{new int{5}};
      ptr = &val; // old memory lost
      ```
    * double allocation
      ```cpp
      int *ptr{new int{5}};
      ptr = new int{6}; // old memory lost
      ```

## new vs malloc
* `new MyClass()` calls MyClass's constructor, `delete` calls desctructor. `malloc(sizeof(MyClass))` just allocates the memory. 
* `malloc` returns void* and is not type-safe. `new` returns pointer of right type.

## Dynamically Allocating Arrays
```cpp
int *arr{new int[len] {}}; // len doesn't have to be a constant
//...
delete[] arr; // array delete

* `new[]` keeps track of how much (how many elements) were allocated, and `delete[]` deallocates all of them
* resizing a dynamically allocated array is not possible, we need to create a new array and copy the old one
* Using `delete` instead of `delete []` on a dynamically allocated array results in undefined behaviour
```

## References
* [learcpp tutorial](https://www.learncpp.com/cpp-tutorial/dynamic-memory-allocation-with-new-and-delete/)
* [iso cpp faq's on freestore management](https://isocpp.org/wiki/faq/freestore-mgmt)
* [isocpp faq: don't delete same pointer twice](https://isocpp.org/wiki/faq/freestore-mgmt#double-delete-disaster)
* [isocpp faq: new vs malloc](https://isocpp.org/wiki/faq/freestore-mgmt#new-vs-malloc)
* [isocpp faq: null check after new?](https://isocpp.org/wiki/faq/freestore-mgmt#new-never-returns-null)
* [isocpp faq: null check before delete?](https://isocpp.org/wiki/faq/freestore-mgmt#delete-handles-null)
* [isocpp faq: why doesn't delete set operand to null?](https://isocpp.org/wiki/faq/freestore-mgmt#delete-zero)
* [initialization notes](tech/cpp/variable_initialization.md)
* [memory management notes](/tech/cpp/memory_management.md)


