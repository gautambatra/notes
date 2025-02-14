# Randomization
## Pseudo Random Number Generator (PRNG)
* Simulates a sequence of random numbers
* Needs a "seed", i.e., an initial starting state
* Different seeds will produce different sequences
    * Need a seed generation algorithm 
    * Number of unique sequences is limited by the number of unique seed values
* Should generate each number with approximately the same probability
* The method by which the next number in the sequence is generated shouldn’t be predictable.
* The PRNG should have a good dimensional distribution of numbers, i.e., a good range (low, medium, high)
* Should have a high period for all seeds. Period is the number of items after which sequence starts repeating.

## Which PRNG to use?
* Out of all PRNGs that ship with C++20, Mersenne Twister algo has the both decent performance and quality
    * But it's outdated by modern standards, and for better algos, third party libraries like Xoshiro family, Wyrand, and Chacha family should be used

## Using the Mersenne Twister
* Defined in `<random>`
* `std::mt19937` for 32-bit numbers, `std::mt19937_64` for 64

### System clock as seed
```cpp
#include <iostream>
#include <random> // for std::mt19937
#include <chrono> // for std::chrono

int main()
{
	// Seed our Mersenne Twister using steady_clock
	std::mt19937 mt{ static_cast<std::mt19937::result_type>(
		std::chrono::steady_clock::now().time_since_epoch().count()
		) };

	// Create a reusable random number generator that generates uniform numbers between 1 and 6
	std::uniform_int_distribution die6{ 1, 6 }; // for C++14, use std::uniform_int_distribution<> die6{ 1, 6 };

	// Print a bunch of random numbers
	for (int count{ 1 }; count <= 40; ++count)
	{
		std::cout << die6(mt) << '\t'; // generate a roll of the die here

		// If we've printed 10 numbers, start a new row
		if (count % 10 == 0)
			std::cout << '\n';
	}

	return 0;
}
```

### Seeding with the random device
```cpp
#include <iostream>
#include <random> // for std::mt19937 and std::random_device

int main()
{
	std::mt19937 mt{ std::random_device{}() };

	// Create a reusable random number generator that generates uniform numbers between 1 and 6
	std::uniform_int_distribution die6{ 1, 6 }; // for C++14, use std::uniform_int_distribution<> die6{ 1, 6 };

	// Print a bunch of random numbers
	for (int count{ 1 }; count <= 40; ++count)
	{
		std::cout << die6(mt) << '\t'; // generate a roll of the die here

		// If we've printed 10 numbers, start a new row
		if (count % 10 == 0)
			std::cout << '\n';
	}

	return 0;
}```
* `std::random_device` is an implementation-defined PRNG, which we can use as the seed value for the Mersenne Twister PRNG. Don't use it on architectures that do not have a properly implemented random_device.


## References
* [learncpp - Introduction to random number generation)](https://www.learncpp.com/cpp-tutorial/introduction-to-random-number-generation/)
* [learncpp - Generating random numbers using  Mersenne twister)](https://www.learncpp.com/cpp-tutorial/generating-random-numbers-using-mersenne-twister/)
