# Halts
## std::exit()
* Causes normal termination, with the argument as the error code (program may be unsuccessful but termination is still normal)
* Performs clean up:
    * destroys static duration objects
    * file clean up if any files were used
* Returns control back to OS with the passed argument as the status code
* Called implicitly at the end of the main function
* Does NOT clean up local variables
* Can use `std::atexit()` to register a clean up function which will be called when std::exit is called. The clean up function:
    * Must have no return value
    * Must have no parameters
* Multiple clean up functions can be registered, the will be called in reverse order of registration
* Note: since std::exit cleans up static variables, it may cause crashes in a multi-threaded program when it is called from one of the threads.
    * To tackle this, use `std::quick_exit` and `std::at_quick_exit` (doesn't clean up static objects)

## std::abort
* Causes abnormal termination

## std::terminate
* Implicitly called on exceptions
* Calls `std::abort` by default

## References
* [learncpp - Halts (exiting your program early)](https://www.learncpp.com/cpp-tutorial/halts-exiting-your-program-early/)
