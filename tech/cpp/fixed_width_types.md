# Fixed Width Types
* `int` is not guaranteed to be of a specific size. Minimum is 2 bytes, but could be 4.
* Can use fixed-width types (defined in `<cstdint>`) when we need a guarantee on the range/size:
    `std::uint8_t`, `std::int16_t`, `std::uint32_t`, `std::int64_t`, etc.

* Many modern compilers treat `std::int8_t` and `std::uint8_t` as `char` and `unsigned char` and may show unintuitive behaviour. Higher width fixed-width types don't have this issue.
* Downsides:
    * Not guaranteed to be defined on all architectures, although it's unlikely that they won't be.
    * Depending on the architecture, a certain fixed-width type may be slower than the standard type

- Another option: the *fast* and *least* types (`#` may be 8, 16, 32, or 64):
  `std::int_fast#_t` and `std::uint_fast#_t`: give the fastest type of width at least # bits
  `std::int_least#_t` and `std::uint_least#_t`: give the smallest type of width at least # bits
  These are guaranteed to exist.
  Should be avoided in favour of fixed-width integers:
    * Implementation defined, so program can have different behaviour on different systems
    * Not used much, so unfamiliarity may lead to confusion/errors
    * Fast types may lead to memory wastage (fast16 may be 32 bits, for example)

* Avoid `short` and `long`, use fixed-width instead.


## std::size_t
* Usually a typedef. Alias for an implementation-defined unsigned integral type. 
    Depending on the compiler, could be unsigned int, unsigned long, or unsinged long long
* If you use std::size_t explicitly in your code, #include one of the headers that defines std::size_t like <cstddef>
* It's the return type of `sizeof`, so the upper limit on the size of a type/object is the maximum value of `size_t`. Actual limit set by the compiler may be much lower.
* Array indexing and size use unsigned values (specifically size_type from the container's class, e.g., std::vector<int>::size_type). Looping through an array while indexing presents challenges:
    * If using unsigned loop index, we may run into infinite loops (if we have something like `for (unsigned i = arr.size(); i >=0; --i)`)
    * If using a signed index, we need to convert/cast to unsigned for indexing or getting size
    * Another way to index: use the underlying C-style array, it accepts signed indices: `arr.data()[index] `
    * Options to deal with this are described in detail on this [learncpp page](https://www.learncpp.com/cpp-tutorial/arrays-loops-and-sign-challenge-solutions/).
* std::array::size_type is always an alias to std::size_t
