# Number Literals
## Specifying Base
```cpp
int x{ 012 }; // 0 before the number means this is octal
int y{ 0xF }; // 0x before the number means this is hexadecimal
int bin{ 0b1010 }; // 0b for binary. Since C++14
```

## Digit Separator `'`
```cpp
int bin { 0b1011'0010 };  // assign binary 1011 0010 to the variable
long value { 2'132'673'462 }; // much easier to read than 2132673462
```

## Outputing in Hexadecimal/Octal
```cpp
std::cout << x << '\n'; // decimal (by default)
std::cout << std::hex << x << '\n'; // hexadecimal
std::cout << x << '\n'; // now hexadecimal
std::cout << std::oct << x << '\n'; // octal
std::cout << std::dec << x << '\n'; // return to decimal
std::cout << x << '\n'; // decimal
```

## Outputting Binary using `std::bitset`
```cpp
std::bitset<8> bin1{ 0b1100'0101 }; // binary literal for binary 1100 0101
std::bitset<8> bin2{ 0xC5 }; // hexadecimal literal for binary 1100 0101

std::cout << bin1 << '\n' << bin2 << '\n';
std::cout << std::bitset<4>{ 0b1010 } << '\n'; // create a temporary std::bitset and print it
```
