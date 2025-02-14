* Casey Muratori says meta programming is good but C++'s way of doing it via templates is BAD. Why? What is the proper way? What are some examples.
* Why is OOP bad?
    Casey:
    * Makes the program rigid, difficult to integrate and extend, because you've already fixed all the abstractions and interfaces. Anything you didn't think about is missing and very difficult to add.
    * Inheritence is bad: 
        * runtime polymorphirsm (virtual functions) are slow. Not just because of the vtable overhead, also because it prevents the compiler from doing some optimizations. See "Clean code horrible performance" for proof
    *
* What is dependency injection?
* What are interfaces (in general)?
* Casey: "I hate the standard library. I write my own standard functions." Why?
* How are the global or parent scope variables stored in the stack frame?
