# R-value references
* Initialised with an r-value only
* Created using a double ampersand
* Cannot be initialised using modifiable or non-modifiable l-values
* R-value reference to a const can be initialised with a non-modifiable r-value
* Extend the lifetime of the r-value to match the lifetime of the r-value reference (lvalue reference to const can also do this)
* Can modify the r-value (if it's modifiable)
```cpp
class Fraction
{
private:
	int m_numerator { 0 };
	int m_denominator { 1 };

public:
	Fraction(int numerator = 0, int denominator = 1) :
		m_numerator{ numerator }, m_denominator{ denominator }
	{
	}

	friend std::ostream& operator<<(std::ostream& out, const Fraction& f1)
	{
		out << f1.m_numerator << '/' << f1.m_denominator;
		return out;
	}
};

int main()
{
	auto&& rref{ Fraction{ 3, 5 } }; // r-value reference to temporary Fraction

	// f1 of operator<< binds to the temporary, no copies are created.
	std::cout << rref << '\n'; // prints 3/5

    int&& rref{ 5 }; // because we're initializing an r-value reference with a literal, a temporary with value 5 is created here
    rref = 10; // modifies the temporary object
    std::cout << rref << '\n'; // prints 10

	return 0;
} // rref (and the temporary Fraction) goes out of scope here
```


## R-value references as function parameters
* Useful for function overloads requiring different behaviour for l-value and r-value arguments.
* Used in move semantics
```cpp
void fun(const int& lref) // l-value arguments will select this function
{
	std::cout << "l-value reference to const: " << lref << '\n';
}

void fun(int&& rref) // r-value arguments will select this function
{
	std::cout << "r-value reference: " << rref << '\n';
}

int main()
{
	int x{ 5 };
	fun(x); // l-value argument calls l-value version of function
	fun(5); // r-value argument calls r-value version of function

	return 0;
}
```


## R-value reference variables are l-values
```cpp
int&& ref{ 5 };
fun(ref);
```
* The above calls the l-value version of `fun`.
* Type and value category of an object are independent.
* `ref` has type `int&&`, but in an expression it's an lvalue, like all named variables.
* `ref` is an lvalue of type `int&&`.
* `func(int&&)` doesn't match because rvalue reference cannot bind to an lvalue (i.e., `ref`).


## References
* [learcpp - rvalue references)](https://www.learncpp.com/cpp-tutorial/rvalue-references/)
