# Types
* Signed/unsigned is just a high-level language concept. They're stored exactly in the same way.
    * Arithmetic operations may differ based on signedness, eg, multiplication. So processor instructions may differ.
    * Addition and subtraction cpu operations don't care about the signs. CPU just sets the sign flag (it's always set, even for unsigned numbers even though we won't use it)
