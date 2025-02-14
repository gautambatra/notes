# "Clean" Code
Most commonly prescribed features:
* Prefer polymorphism to if/switch statements
* Keep internals of objects hidden (use of private members, virtual functions)
* Functions should be small
* Functions should do *one thing*
* DRY - don't repeat yourelf.

## Drawbacks
* If using a class hierarchy with virtual function overrides, different kind of objects/data are grouped together, with there specialised functions most of the times in different files. In the non-"Clean Code" style, you can group/organise based on functionality, and then possibly find patterns to write that functionality in a more performant way.
* Having functionality behind virtual functions prevents the compiler from understanding your code, so it can't optimise it much.
* Incorporating or enabling cpu features like SSE, SIMD, etc. is extremely difficult with "Clean Code."

## References
* [Casey Muratori - Clean Code, Horrible Performance](https://www.youtube.com/watch?v=tD5NrevFtbU)
* [clean code horrible performance transcript](/tech/performance_aware_programming/009_clean_code_horrible_performance.md)
