# Move semantics

## Motivation
* Move semantics = transfer of ownership of an object rather than making a copy
* `std::auto_ptr` was implemented something like the following:
    ```cpp
    template <typename T>
    class Auto_ptr
    {
    	T* m_ptr {};
    public:
    	Auto_ptr(T* ptr=nullptr)
    		:m_ptr(ptr)
    	{
    	}
    
    	~Auto_ptr()
    	{
    		delete m_ptr;
    	}
    
    	// A copy constructor that implements move semantics
    	Auto_ptr(Auto_ptr& a) // note: not const
    	{
    		// We don't need to delete m_ptr here.  This constructor is only called when we're creating a new object, and m_ptr can't be set prior to this.
    		m_ptr = a.m_ptr; // transfer our dumb pointer from the source to our local object
    		a.m_ptr = nullptr; // make sure the source no longer owns the pointer
    	}
    
    	// An assignment operator that implements move semantics
    	Auto_ptr& operator=(Auto_ptr& a) // note: not const
    	{
    		if (&a == this)
    			return *this;
    
    		delete m_ptr; // make sure we deallocate any pointer the destination is already holding first
    		m_ptr = a.m_ptr; // then transfer our dumb pointer from the source to the local object
    		a.m_ptr = nullptr; // make sure the source no longer owns the pointer
    		return *this;
    	}
    
    	T& operator*() const { return *m_ptr; }
    	T* operator->() const { return m_ptr; }
    	bool isNull() const { return m_ptr == nullptr; }
    };
    ```
    * Move semantics were implemented via copy constructor and assignment operator.
    * Issues:
        * If this object is passed by value, the ownership is transferred to the function parameter and the caller is left with null, losing the data.
        * `std::auto_ptr` always deleted using non-array delete, so didn't work properly with dynamic arrays.
        * Didn't work well with STL classes and algorithms, because most of them assume that a copy operation makes an actual copy rather than a move
* For the above reasons, `std::auto_ptr` was deprecated in C++11 and removed in C++17.
* C++11 formally defined the concept of *move* and introduced *move semantics*
* Note: in the above example, we could have done deep copies in the copy constructor and assignment operator to mitigate some of the issues just described (passing by value, returning by value), but it would lead to extra allocation/deallocation:
```cpp
Auto_ptr(const Auto_ptr& a)
{
    m_ptr = new T;
    *m_ptr = *a.m_ptr;
}
Auto_ptr& operator=(const Auto_ptr& a)
{
    // Self-assignment detection
    if (&a == this)
        return *this;

    // Release any resource we're holding
    delete m_ptr;

    // Copy the resource
    m_ptr = new T;
    *m_ptr = *a.m_ptr;

    return *this;
}
//...
Auto_ptr<Resource> generateResource()
{
	Auto_ptr<Resource> res{new Resource};
	return res; // this return value will invoke the copy constructor
}
//...
Auto_ptr<Resource> mainres;
mainres = generateResource(); // this assignment will invoke the copy assignment
```
* In the above example, the `generateResource` itself allocates a new object then calls the copy constructor to return by value (and create the temporary object for the RHS in the assignment statement), and the assignment calls the copy assignment, leading to 3 objects being allocated/deallocated for just `mainres = generateResource()`


## Move constructor and Move Assignment
* Analogous to copy constructor and copy assignment.
* Instead of const l-value reference, parameter is rvalue reference. [[r-value-references ]].
    * l-value not a good idea as `a = b` is not expected to change b if b is an l-value
    * r-value is a temporary object, so changing it is okay (it would've anyway been destroyed)
* Goal is to transfer ownership of resources instead of creating copies.
* Called when 
    1. The move constructor/move assignment are defined, AND
    2. the argument for construction and assignment is an rvalue (usually a literal or a temporary).
  Otherwise, copy constructor/copy assignment are called.
* Should be marked as `noexcept` (see [[noexcept]]).


```cpp
// Move constructor
// Transfer ownership of a.m_ptr to m_ptr
Auto_ptr4(Auto_ptr4&& a) noexcept
: m_ptr(a.m_ptr)
{
    a.m_ptr = nullptr; // we'll talk more about this line below
}

// Move assignment
// Transfer ownership of a.m_ptr to m_ptr
Auto_ptr4& operator=(Auto_ptr4&& a) noexcept
{
    // Self-assignment detection
    if (&a == this)
        return *this;

    // Release any resource we're holding
    delete m_ptr;

    // Transfer ownership of a.m_ptr to m_ptr
    m_ptr = a.m_ptr;
    a.m_ptr = nullptr; // we'll talk more about this line below

    return *this;
}
```
* Now, for `mainres = generateResource();`, there's only 1 object created (in generateResource), on return that object is transferred to the temporary variable, and on assignment to mainres.
* Only one object is created and destroyed (instead of 3). No deep copy happens either.
* Inside `generateResource()`, res is returned by value, so a temporary object is created and the ownership of the new object created is transferred from res to the temporary object. When res goes out of scope nothing happens as it no longer holds an object. 
    * ***Move copy is done here, even though res is an lvalue, because, the C++ specification has a special rule that says automatic objects returned from a function by value can be moved even if they are l-values. This is because the the automatic object is going to be destroyed anyway. ***
* For the `mainres = generateResource();` assignment, the temporary object created by the `generateResource()` call is move assigned to `mainres`, so the ownership is transferred from the temporary object to `mainres`. When the assignment ends, temporary object goes out of the expression scope and is destroyed, but it no longer manages any pointer.
* In this example, we should mark the copy assignment and copy constructor as `delete` to disable copying. Then, if an Auto_ptr4 lvalue is passed to a function by value, compiler will complain about no copy constructor being there. This is good, because we should be passing it as const lvalue reference.
* Note: With C++17, copy elision is mandatory, so for most cases of passing by value and returning by value, the copy constructor will not be called. 

## Move Constructor/Move Assignment are Called When:
1. They are defined, AND
2. The argument is an rvalue, e.g., a literal or a temporary object

## Implicit move constructor and move assignment
* Created by compiler if:
    1. There are no user-declared copy constructors or copy assignment operators. AND
    2. There are no user-declared move constructors or move assignment operators. AND
    3. There is no user-declared destructor.
* Do a member-wise move:
    * If a member has a move constructor/move assignment, it will be invoked, otherwise copies will be made
    * **Pointers will be copied, not moved**: To move pointer members, we must define the move constructor/move assignment for those member types.
* Note: if copy constructor is deleted, implicit move constructor is not created. Hence when deleting copy constructor, explicitly delete or define move constructor to avoid confusion.

## Rule of 5
**If the copy constructor, copy assignment, move constructor, move assignment, or destructor are defined or deleted, then each of those functions should be defined or deleted.**

* Note: If move constructor and move assignment are deleted, return by value may lead to compilation error, because a deleted function is still a candidate for overloading:
```cpp
class Name
{
private:
    std::string m_name {};

public:
    Name(std::string_view name) : m_name{ name }
    {
    }

    Name(const Name& name) = default;
    Name& operator=(const Name& name) = default;

    Name(Name&& name) = delete;
    Name& operator=(Name&& name) = delete;

    const std::string& get() const { return m_name; }
};

Name getJoe()
{
    Name joe{ "Joe" };
    return joe; // error: Move constructor was deleted
}
```

## Why Move Semantics
* For `a=b`, where b is an lvalue, we can't employ move semantics because b is expected to be valid and may be used after this assignment 
* If the argument is an rvalue, it's not expected to be valid after the assignment/copy, so moving ownership is better as it is cheaper (**optimization opportunity**)

## Why Set Original Owner to nullptr
* When the rvalue is destroyed, its pointer will be deleted
* If not set to nullptr during after move copy/move assignment, the moved pointer will be destroyed, leading to a dangling pointer with the new owner.

## Using a swap Function 
* Can use a swap function to implement move constructor and move assignment, similar to copy and swap idiom (See notes [[copy-and-swap-idiom]])
* Must not use `std::swap` as it will in turn call the move constructor and move assignment leading to an infinite recursion.
```cpp
class Name
{
private:
    std::string m_name {};

public:
    Name(std::string_view name) : m_name{ name } {}

    Name(const Name& name) = delete;
    Name& operator=(const Name& name) = delete;

    // Create our own swap friend function to swap the members of Name
    friend void swap(Name& a, Name& b) noexcept
    {
        // We avoid recursive calls by invoking std::swap on the std::string member,
        // not on Name
        std::swap(a.m_name, b.m_name);
    }

    Name(Name&& name) noexcept
    {
        std::cout << "Move ctor\n";
        swap(*this, name); // Calling our swap, not std::swap
    }

    Name& operator=(Name&& name) noexcept
    {
        std::cout << "Move assign\n";
        swap(*this, name); // Calling our swap, not std::swap
        return *this;
    }

    const std::string& get() const { return m_name; }
};

```


## std::move
* Casts its argument to an rvalue reference, using static_cast, AKA xvalue
* Enables the use of move semantics on lvalues (moving is more efficient than copying)
```cpp
// Example 1: Swap
template <typename T>
void mySwapMove(T& a, T& b)
{
	T tmp { std::move(a) }; // invokes move constructor
	a = std::move(b); // invokes move assignment
	b = std::move(tmp); // invokes move assignment
}

//Example 2: push_back
int main()
{
	std::vector<std::string> v;

	// We use std::string because it is movable (std::string_view is not)
	std::string str { "Knock" };
	v.push_back(str); // calls l-value version of push_back, which copies str into the array element
    v.push_back(std::move(str)); // calls r-value version of push_back, which moves str into the array element
}
```

* As per the C++ standard, *"moved-from objects \[of types defined in the C++ standard library\] shall be placed in a valid but unspecified state."*
    - The value of the moved-from object will be implementation dependent
    - Therefore, we may reset or set such an object to other values and re-use it, but we should not call any function that relies on the older (moved) value (before resetting/reassigning).

* Other possible uses of std::move:
    - in sorting (eg swapping values for bubble sort)
    - to transfer resources from one smart pointer to another

## std::move_if_no_except
* Constructors should uphold the strong exception guarantee so that if construction fails the program is not left in an altered state
* If a move constructor fails/throws an exception, the original object (to be moved) may get destroyed/corrupted, leaving the program state altered.
* `std::move_if_no_except` makes the compiler check if the move has a strong exception guarantee, i.e., if the move constructor is `noexcept`. If that is the case (or if copy constructor doesn't exist), it is identical to move. Otherwise, it converts the argument to an lvalue reference, leading to a copy construction instead of move.
* STL uses std::move_if_no_except often. For example, for operations like `resize`, `std::vector` uses move constructors only if the element has a noexcept move constructor

## References
* [learcpp - introduction to smart pointers and move semantics](https://www.learncpp.com/cpp-tutorial/introduction-to-smart-pointers-move-semantics/)
* [learcpp - move constructors and move assignments](https://www.learncpp.com/cpp-tutorial/move-constructors-and-move-assignment/)
* [learcpp - std::move](https://www.learncpp.com/cpp-tutorial/stdmove/)
* [learcpp - std::move_if_no_except](https://www.learncpp.com/cpp-tutorial/stdmove_if_noexcept/)
* [learcpp - Class initialization and copy elision](https://www.learncpp.com/cpp-tutorial/class-initialization-and-copy-elision/)
