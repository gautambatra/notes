# cin
## Ignoring extra characters: `cin.ignore`
`std::cin.ignore(num, delim)`: Ignore num characters until the delim character

**Example:**
```cpp
#include <limits> // for std::numeric_limits

void ignoreLine()
{
    std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n');
}

double getDouble()
{
    std::cout << "Enter a decimal number: ";
    double x{};
    std::cin >> x;

    ignoreLine();
    return x;
}
```

## Detecting extraneous input: `cin.eof()` and `cin.peek()`
`std::cin.eof()`: returns true if the last input operation reached the end of the input stream
`std::cin.peek()`: peek at the next character in the input stream without extracting it

**Example:**
```cpp
// returns true if std::cin has unextracted input on the current line, false otherwise
bool hasUnextractedInput()
{
    return !std::cin.eof() && std::cin.peek() != '\n';
}

double getDouble()
{
    while (true) // Loop until user enters a valid input
    {
        std::cout << "Enter a decimal number: ";
        double x{};
        std::cin >> x;

        // If there is extraneous input, treat as failure case
        if (hasUnextractedInput())
        {
            ignoreLine(); // remove extraneous input
            continue;
        }

        return x;
    }
}
```

## Detecting failed extraction: `cin.fail()`
If an extraction fails, e.g., a character is entered where float is expected, `cin` goes into fail mode and all subsequent extractions silently fail unless the failure is detected and `cin` is put back into normal mode by calling `clear()`

`if (std::cin.fail())` can be written as `if (std::cin)` as `cin` has boolean conversion

**Example:**
```cpp
double getDouble()
{
    while (true) // Loop until user enters a valid input
    {
        std::cout << "Enter a decimal number: ";
        double x{};
        std::cin >> x;

        if (!std::cin) // If the previous extraction failed
        {
            // Let's handle the failure
            std::cin.clear(); // Put us back in 'normal' operation mode
            ignoreLine();     // And remove the bad input
            continue;
        }

        // Our extraction succeeded
        ignoreLine(); // Ignore any additional input on this line
        return x;     // Return the value we extracted
    }
}
```

We can also call `clear()` and `ignore()` without checking for failure:
```cpp
double getDouble()
{
    while (true) // Loop until user enters a valid input
    {
        std::cout << "Enter a decimal number: ";
        double x{};
        std::cin >> x;

        bool success { std::cin }; // Remember whether we had a successful extraction
        std::cin.clear();          // Put us back in 'normal' operation mode (in case we failed)
        ignoreLine();              // Ignore any additional input on this line (regardless)

        if (success)               // If we actually extracted a value
            return x;              // Return it (otherwise, we go back to top of loop)
    }
}
```

## Detecting EOF: `cin.eof()`
```cpp
bool clearFailedExtraction()
{
    // Check for failed extraction
    if (!std::cin) // If the previous extraction failed
    {
        if (std::cin.eof()) // If the user entered an EOF
        {
            std::exit(0); // Shut down the program now
        }

        // Let's handle the failure
        std::cin.clear(); // Put us back in 'normal' operation mode
        ignoreLine();     // And remove the bad input

        return true;
    }

    return false;
}
```

## References
* [learncpp - std::cin and handling invalid input](https://www.learncpp.com/cpp-tutorial/stdcin-and-handling-invalid-input/)
