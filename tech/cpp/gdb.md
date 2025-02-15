# GDB

## Quick Reference
```gdb
# help
apropos <command/text>
help <command>

# run command on breakpoint
command <breakpoint num>

# info about local vars
info locals

# function arguments
info args

# run until line number
u/until <line num>

# ignore breakpoint for next count occurrences
ignore <breakpoint num> <count>

# delete all breakpoints in function
clear <function>

# delete all breakpoints at a line
clear <line num>

# printing variables
p/t <var>  # binary format
p/x <var>  # hexadecimal format
p/c <var>  # char format
p/u <var>  # unsigned format

# update value of variable
set <var> = <val>

# print enum value
p/d <enum_literal> # d for decimal

# call a function
call <function name>

# info of variables matching regex
info variables <regex>

# show all threads
info threads

# show call stack for all threads
# (can use any named command instead of bt)
thread apply all bt 
taa bt

# switch to thread number num
thread <num>
th <num>

# remain in current thread
set scheduler-locking on

# specify which libthread_db to use 
set libthread-db-search-path <path>

# allow auto-loading
add-auto-load-safe-path ~/.gdbinit

# set backtrace limit to unlimited
set backtrace limit 0

# pretty print objects
set print pretty on

# print C++ names in assembly listings in demangled form
set print asm-demangle on

# print object's derived type based on vtable info
set print object on

# print C++ virtual function tables
set print vtbl on

# record command history in a file
set history save on

# number of commands to save in history
set history size 10000

# specify command history file
set history filename ~/.gdb_history

# send debug output to a file
set logging on 

# set log file
set logging file <file name>

# print full strings/containers
set print elements 0

# pretty print arrays (not sure where this applies)
set print array on

# print the source file name and line number of a symbol
set print symbol-filename on

# Local settings
source ~/.config/home/gdbinit
```

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
