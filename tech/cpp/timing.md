# Timing
## Quickstart
```cpp
std::cout<<std::thread::hardware_concurrency()<<std::endl;
std::chrono::high_resolution_clock::time_point t1 = std::chrono::high_resolution_clock::now();
myfunc();
std::chrono::high_resolution_clock::time_point t2 = std::chrono::high_resolution_clock::now();

std::chrono::duration<double> time_span = std::chrono::duration_cast<std::chrono::duration<double>>(t2-t1);
std::cout << "Time taken in myfunc: " << time_span.count() << " seconds\n";
```
