# constexpr if statements
* Condition is evaluated at compile time
* false part is optimised out

## Example without constexpr
```cpp
template<typename T>
auto length(const T& value) noexcept {
    if (std::is_integral<T>::value) { // is number
        return value;
       }
    else{
        return value.length();
    }
}

int main() noexcept {
    int a = 5;
    std::string b = "foo";

    std::cout << length(a) << ' ' << length(b) << '\n'; // doesn't compile
}
```
* Results in a compilation error: `error: request for member 'length' in 'val', which is of non-class type 'const int' return val.length();` because of the following instantiation:
```cpp
auto length(const int& value) noexcept {
    if (std::is_integral<int>::value) { // is number
        return value;
    else
        return value.length();
}
```

## Example with constexpr
```cpp
template<typename T>
auto length(const T& value) noexcept {
    if constexpr (std::is_integral<T>::value) { // is number
        return value;
    else
        return value.length();
}
```
* Now the instantiations are:
```cpp
int length(const int& value) noexcept {
    //if constexpr (std::is_integral<int>::value) { this branch is taken
        return value;
    //else                           discarded
    //    return value.length();     discarded
}

std::size_t length(const std::string& value) noexcept {
    //if constexpr (std::is_integral<int>::value) { discarded
    //    return value;                   discarded
    //else                           this branch is taken
        return value.length();
}
```

# References
* [learncpp - constexpr if statments](https://www.learncpp.com/cpp-tutorial/constexpr-if-statements/)
* [stackoverflow - constexpr if vs if](https://stackoverflow.com/questions/43434491/difference-between-if-constexpr-vs-if)
