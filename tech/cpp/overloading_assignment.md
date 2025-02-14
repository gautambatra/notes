# Overloading the Assignment Operator

## Points to Remember
* Should be declared as a member function
* Must handle self assignment, otherwise memory leaks occur if assignment involed dynamic memory allocation
```cpp
class MyString
{
private:
	char* m_data {};
	int m_length {};

public:
    // ... (constructors, etc)

	// Overloaded assignment
	MyString& operator= (const MyString& str);
};

MyString& MyString::operator= (const MyString& str)
{
	// self-assignment check
	if (this == &str) // pointer comparison, no == overload required
		return *this;

	// if data exists in the current string, delete it
	if (m_data) delete[] m_data;

	m_length = str.m_length;
	m_data = nullptr;

	// allocate a new array of the appropriate length
	if (m_length)
		m_data = new char[static_cast<std::size_t>(str.m_length)];

	std::copy_n(str.m_data, m_length, m_data); // copies m_length elements of str.m_data into m_data

	// return the existing object so we can chain this operator
	return *this;
}

```
* we set m_data to nullptr after deleting it because if there is an exception after the delete, the destructor for m_data may be called on the already deleted object.
* Skip self-assignment check for:
    - copy constructors (new object needs to be created)
    - if class can naturally handle self assignment, eg., if all the members are of int type
* Better way of handling self-assignment: copy and swap idiom (See notes [[copy-and-swap-idiom]])
* Compiler provides an implicit implementation of the assignment operator if one is not specified and it is not defined as `delete`.
* The implicit implementation will be defined as deleted if the class has const members

## References
* [learncpp - Overloading the Assignment Operator](https://www.learncpp.com/cpp-tutorial/overloading-the-assignment-operator/)
