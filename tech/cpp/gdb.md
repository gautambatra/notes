# GDB

## Showing Disassembly
`disas`
```
(gdb) disas
Dump of assembler code for function main():
   0x000055555555516d <+0>:     endbr64
   0x0000555555555171 <+4>:     push   rbp
   0x0000555555555172 <+5>:     mov    rbp,rsp
   0x0000555555555175 <+8>:     sub    rsp,0x10
=> 0x0000555555555179 <+12>:    mov    BYTE PTR [rbp
   0x000055555555517d <+16>:    add    BYTE PTR [rbp
   0x0000555555555181 <+20>:    call   0x55555555514
   0x0000555555555186 <+25>:    mov    eax,0x0
   0x000055555555518b <+30>:    leave
   0x000055555555518c <+31>:    ret
```
* The number in the first column is the address in the memory of the corresponding line of code

## Setting Working Directory
`set cwd <dir>`

## Stepping through Assembly
`set disassemble-next-line on`

## Setting Disassembly Flavour
`set disassembly-flavor intel`

## Print Register Values
`info registers` : print all registers
`info registers eax` : show register eax

## info proc
`info proc` : summarise information about process
`info proc mappings` : show memory address space ranges
`info proc status` : show detailed process info including pid, gid, virtual memory usage, signals, time usage, thread usage

## Examining Memory
`x/nfu <addr>` 
    n - number of addresses to print (defualt : 1)
    f - display format (x/d/o/etc.) (default : x)
    u - unit size (b/h/w/g) (default : b, i.e., Bytes)
`x <address>` : show the contents at the given memory address
`x/<num> <address>` : show the contents of <num> memory addresses starting from <address>


## To figure out
* How to (pretty) display elements of an array or pointer used as an array (C-style or otherwise)
    * Same with fields of a struct/class
