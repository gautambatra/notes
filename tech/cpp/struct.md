# Struct
* Size of a struct is at least the sum of the sizes of its members. There could be padding for each member for better alignment. To minimise size, declare the members in decreasing order of size.
    * Compiler could be adding the padding based on what the CPU prefers, e.g., it may process data with 32 bit boundaries more efficiently 
* If a struct object is uninitialised, any members with no default initialisation would remain uninitialised.
* If a struct object is value initialised, i.e., `{}`, members with no default initialisation will be value initialised.
