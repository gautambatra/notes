# Miscellaneous C++ Notes
* Short circuiting: When a condition is not evaluated at all, for example, in `bool a = func1() || func2()`, if func1() returns a nonzero value in the example below, func2() will not be called:

