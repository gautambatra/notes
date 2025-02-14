# Copy and Swap Idiom
## When to use it?
* When overloading the copy assignment operator for a class, especially one that manages an object/data, such as a wrapper class.

## Steps
* Uses the copy constructor to create a local temporary object
* Uses a `swap` function to swap the temporary and the old data of the object to be updated
* Destructs the temporary (which now contains the old data)

## Requirements
* A copy constructor
* A destructor
* A `swap` function: a non-throwing function that swaps two objects, member for member
    - Cannot use `std::swap` directly as that uses copy assignment operator itself.

## Problems with the non-copy-and-swap method
```cpp
dumb_array& operator=(const dumb_array& other)
{
    if (this != &other) // (1)
    {
        // get rid of the old data...
        delete [] mArray; // (2)
        mArray = nullptr; // (2) *(see footnote for rationale)

        // ...and put in the new
        mSize = other.mSize; // (3)
        mArray = mSize ? new int[mSize] : nullptr; // (3)
        std::copy(other.mArray, other.mArray + mSize, mArray); // (3)
    }

    return *this;
}
```
* Self-assignment check] is required (See notes [[overloading-the-assignment-operator]]). Self assignment is a rare occurrence and this has performance overhead.
* We first destruct the old (current) object and use the copy constructor to create and copy the new object. Construction is error prone and may fail, leading to a deleted old object. Copy-and-swap first creates the object and then swaps it with the old one only if the construction is succesful.
* There is code duplication. Copy constructor and destructor already have this implementation.


## The copy-and-swap Implementation
```cpp
class dumb_array
{
public:
    // ...
friend void swap(dumb_array& first, dumb_array& second) // nothrow
    {
        // enable ADL (not necessary in our case, but good practice)
        using std::swap;

        // by swapping the members of two objects,
        // the two objects are effectively swapped
        swap(first.mSize, second.mSize);
        swap(first.mArray, second.mArray);
    }
    // ...
};

dumb_array& operator=(dumb_array other) // (1)
{
    swap(*this, other); // (2)

    return *this;
}
```
* Note that in the operator overload other is passed by value. 
    - This ensures that the code enters the function only if the copy constructor to copy the passed object to `other` succeeds. Gives us a strong exception free guarantee. 
    - If passed as const reference, we would have to create a temp object explicitly in the function by calling the copy constructor. This code duplication is also avoided.
    - If you are going to create a copy anyway, better to do it in the parameter list
* `swap` is non-throwing, so now the entire operation is safe. Therefore, this should be done for classes where a non-throwing swap can be written. If the swap implementation involves copying data to a temporary object, we would be using the copy constructor or copy assignment which may throw.
* Need a move constructor for cases where `other` is being initialised with an r-value reference.
* Can be inefficient if self assignment is common and construction is expensive
* The swap is there to be able to reuse the destructor, so you don't have to write any cleanup code in your assignment operators.
* If there's no cleanup to be done and only assignment, then you should be able to declare the assignment operators as default and copy-and-swap isn't needed.

## References
* [StackOverflow - what is the copy and swap idiom](https://stackoverflow.com/questions/3279543/what-is-the-copy-and-swap-idiom)
