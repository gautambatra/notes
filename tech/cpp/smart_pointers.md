# Smart Pointers
* Should always be allocated on the stack, so that whenever it goes out of scope (normally or via an exception), the managed object is deallocated

## std::unique_ptr
* Used to manage an object not shared by multiple object, i.e., completely owns the object
* Implements move semantics properly, unlike auto_ptr (See notes [[move-semantics]])
* Copy initialization and copy assignment are disabled. Must use move semantics.
* Operator* returns a reference to the managed resource, and operator-> returns a pointer.
* May be null, i.e., not be managing any object if constructed using default constructor, or if ownership is transferred using std::move. Has a cast to bool that returns true if the std::unique_ptr is managing a resource
* Knows when to use scalar delete vs vector delete (unlike auto_ptr), but its almost always better to use `std::array`, `std::vector` or `std::string` for arrays/strings.

### std::make_unique
* Can be created directly, eg: `std::unique_ptr<Myclass> a {new Myclass}`, or using `std::make_unique`, for simplicity and better exception guarantee, eg: `auto f1{ std::make_unique<Fraction>(3, 5) };`
    - eg: `some_function(std::unique_ptr<T>(new T), function_that_can_throw_exception());`
        Here if the `new T` happens first, followed by `function_that_can_throw_exception` and then the unique_ptr creation, the exception may lead to memory leak as the unique_ptr is not yet created so T will not be deallocated. This was fixed in C++17 as interleaving of evaluation of function argument is no longer permitted. However, make_unique doesn't have this problem at all as the order of creation of T then unique_ptr is fixed inside make_unique.
    - `std::make_unique` was introduced in C++14

### Passing std::unique_ptr to a function
* If passing by value directly, must use move semantics. Ownership will be transferred:
```cpp
// This function takes ownership of the Resource, which isn't what we want
void takeOwnership(std::unique_ptr<Resource> res)
{
     if (res)
          std::cout << *res << '\n';
} // the Resource is destroyed here

int main()
{
    auto ptr{ std::make_unique<Resource>() };
//    takeOwnership(ptr); // This doesn't work, need to use move semantics
    takeOwnership(std::move(ptr)); // ok: use move semantics

    return 0;
```

* If the resource is needed after the function call, can pass unique_ptr by const reference, or better yet, to keep the function agnostic, can pass the raw managed pointer using `get`:
```cpp
void useResource(const Resource* res)
{
    // use res
}

int main()
{
	auto ptr{ std::make_unique<Resource>() };
	useResource(ptr.get()); // note: get() used here to get a pointer to the Resource
    return 0;
}
```

### Class members as unique_ptr
* Members can be declared as unique_ptr. This way, they are automatically destructed when class destructor is called.
* Will never be deallocated if class destructor is missed


### Misuse of unique_ptr
1. Multiple objects managing the same resource
```cpp
Resource* res{ new Resource() };
std::unique_ptr<Resource> res1{ res };
std::unique_ptr<Resource> res2{ res };
```
Here when res1 and res2 go out of scope, both will try to deallocate res, causing undefined behaviour.

2. Manually deleting the resource
```cpp
Resource* res{ new Resource() };
std::unique_ptr<Resource> res1{ res };
delete res;
```
Here, when res1 goes out of scope it will also try to delete res.

## std::shared_ptr
* Used when multiple smart pointers are required to manage the same resource.
* Internally keeps track of number of owners. Resource is deallocated only when the last shared_ptr goes out of scope or is assigned to something else.
* Must create the subsequent shared_ptrs using an existing shared_ptr. See code comment:
```cpp
{
    Resource* res { new Resource };
	std::shared_ptr<Resource> ptr1{ res };
	{
		std::shared_ptr<Resource> ptr2 { ptr1 }; // make another std::shared_ptr pointing to the same thing
		// std::shared_ptr<Resource> ptr2 { res }; !!! if created like this, res would be destoyed on exiting this scope itself, causing crash/UB when it is destructed again later because of ptr1 going out of the outer scope
	} // ptr2 goes out of scope here, but nothing happens
} // res destructed
```
* Can be null, like unique_ptr
* Can convert a `unique_ptr` to `shared_ptr` using the shared_ptr ctor that accepts a unique_ptr rvalue. The ownership will be transferred to the shared_ptr. Reverse cannot be done.
    - Therefore, when returning a smart pointer from a function, always return a unique_ptr.
* Till C++17, shared_ptr cannot manage an array properly. Fixed in C++20.

### std::make_shared
* Analogous to make_uniqe
* Introduced in C++14
* Leads to safer, simpler and more performant code
```cpp
{
	// allocate a Resource object and have it owned by std::shared_ptr
	auto ptr1 { std::make_shared<Resource>() };
	{
		auto ptr2 { ptr1 }; // create ptr2 using copy of ptr1
        // ...
	} // ptr2 goes out of scope here, but nothing happens
}
```
* Internally, shared_ptr has 2 pointers, the managed resource, and a control block containing a bunch of information, including the number of shared_ptrs managing the resource. When using the shared_ptr constructor, these two are allocated separately, but with `std::make_shared`, there is a single memory allocation, leading to better performance.
* When independently creating 2 shared_ptrs for the same resource, the control blocks are allocated separately, leading the issue described in the previous sub-section. If using the copy constructor/copy assignment as in the snippet above, the control block is correctly updated.

### Cyclic Ownership
* Caused when you have a series of shared_ptrs that point to the resource of the next shared_ptr and the last shared_ptr points to the resource of the first one. 
* Results in memory leaks, as even when the shared_ptrs go out of scope, there is one still owing it, so the resource never gets destructed. 
* Example with 2:
```cpp
class Person
{
	std::string m_name;
	std::shared_ptr<Person> m_partner; // initially created empty

public:
	Person(const std::string &name): m_name(name)
	{
		std::cout << m_name << " created\n";
	}
	~Person()
	{
		std::cout << m_name << " destroyed\n";
	}

	friend bool partnerUp(std::shared_ptr<Person> &p1, std::shared_ptr<Person> &p2)
	{
		p1->m_partner = p2;
		p2->m_partner = p1;
		std::cout << p1->m_name << " is now partnered with " << p2->m_name << '\n';
		return true;
	}
};

int main()
{
	auto lucy { std::make_shared<Person>("Lucy") }; // create a Person named "Lucy"
	auto ricky { std::make_shared<Person>("Ricky") }; // create a Person named "Ricky"
	partnerUp(lucy, ricky); // Make "Lucy" point to "Ricky" and vice-versa
	return 0;
}
```
The above program prints the following only (no destructors are called):
```
Lucy created
Ricky created
Lucy is now partnered with Ricky
```


## std::weak_ptr
* Solves the circular dependency problem
* Only an observer/user, not an owner
* Cannot access the managed object directly using a weak_ptr, need to convert to shared_ptr by calling the `lock` member function
```cpp
lass Person
{
	std::string m_name;
	std::weak_ptr<Person> m_partner; // note: This is now a std::weak_ptr

public:

	Person(const std::string &name): m_name(name)
	{
		std::cout << m_name << " created\n";
	}
	~Person()
	{
		std::cout << m_name << " destroyed\n";
	}

	friend bool partnerUp(std::shared_ptr<Person> &p1, std::shared_ptr<Person> &p2)
	{
		if (!p1 || !p2)
			return false;

		p1->m_partner = p2;
		p2->m_partner = p1;

		std::cout << p1->m_name << " is now partnered with " << p2->m_name << '\n';

		return true;
	}
};
int main()
{
	auto lucy { std::make_shared<Person>("Lucy") };
	auto ricky { std::make_shared<Person>("Ricky") };

	partnerUp(lucy, ricky);

	return 0;
}
```
Now, when ricky goes out of scope, there is no other shared_ptr pointing to the same Person pointer, just a weak_ptr, so it is properly destructed. Same with lucy.
* Keeps a reference count, so using the `expired` member function, we can check if the managed object is still alive (and not dangling). Dumb pointers cannot detect whether a non-nullptr pointer has been deallocated.

## References
* [learncpp - std::unique_ptr](https://www.learncpp.com/cpp-tutorial/stdunique_ptr/)
* [learncpp - std::shared_ptr](https://www.learncpp.com/cpp-tutorial/stdshared_ptr/)
* [learncpp - Circular dependency issues with std::shared_ptr, and std::weak_ptr](https://www.learncpp.com/cpp-tutorial/circular-dependency-issues-with-stdshared_ptr-and-stdweak_ptr/)
