# Designing Your Program
## Design
1. Define your goal
2. Define the requirements: "what", not "how"
3. Define tools, architecture, targets, testing strategy, version control
4. Define sub-tasks
    * Top down approach: keep breaking bigger tasks into sub-tasks, creating a hierarchy
    * Bottom up approach: start with easy targets, group them to form the hierarchy
5. Define sequence of tasks

## Implementation
1. Outline your main function (what function calls exist in main)
2. Implement each function, break complex functions into simpler ones
    a. Define the prototype
    b. Write the function
    c. Test the function
3. Test the whole program


## Tips

* Make your initial goal simple to begin with
* Add features over time
* Focus on one task at a time
* Follow best practices.
* Don’t let your functions get too long: ideally less than 10 lines
* Prefer using the standard library to writing your own code, when possible.
* Comment your code liberally.
* Avoid clever/non-obvious solutions.
* Test each piece of code as you implement it
* Don’t invest in perfecting early code
* Optimize for maintainability, not performance. There is a famous quote (by Donald Knuth) that says “premature optimization is the root of all evil”. New programmers often spend far too much time thinking about how to micro-optimize their code (e.g. trying to figure out which of 2 statements is faster). This rarely matters. Most performance benefits come from good program structure, using the right tools and capabilities for the problem at hand, and following best practices. Additional time should be used to improve the maintainability of your code. Find redundancy and remove it. Split up long functions into shorter ones. Replace awkward or hard to use code with something better. The end result will be code that is easier to improve and optimize later (after you’ve determined where optimization is actually needed) and fewer bugs.
* When making changes to your code, make behavioral changes OR structural changes, and then retest for correctness. Making behavioral and structural changes at the same time tends to lead to more errors as well as errors that are harder to find.
* *Defensive programming*: a practice whereby the programmer tries to anticipate all of the ways the software could be misused, either by end-users, or by other developers using the code. These misuses can often be detected and then mitigated
* Use a static analysis tool on your programs to help find areas where your code is non-compliant with best practices. See references for list of tools.

## Resources/References
* [learncpp - How to design your first programs](https://www.learncpp.com/cpp-tutorial/how-to-design-your-first-programs/)
* [learncpp - Finding issues before they become problems](https://www.learncpp.com/cpp-tutorial/finding-issues-before-they-become-problems/)
* [Wikipedia - Static analysis tools](https://en.wikipedia.org/wiki/List_of_tools_for_static_code_analysis#C,_C++)
* [learncpp - Introduction to fundamental data types](https://www.learncpp.com/cpp-tutorial/introduction-to-fundamental-data-types/)
