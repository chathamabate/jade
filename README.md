# jade

Interpreter Side Project!

## Concept
The end goal is an interpreter for a higher 
level language. The first task will be to 
create some basic form of the language which 
can be interpreted quickly.
The higher level language will be transformed into 
this intermediate representation before being run.

## IR and Interpretation
The interpreter will have some notion of a stack. 
This will store activation records of called functions.
There will also be dynamic memory.

Static distance form will be used for stack 
referencing. 

### Datatypes
There will be 2 primitive datatypes.
* `byte` 8-bit value.  (Character or Boolean)
* `quad` 32-bit value. (Signed Integer or Float)

The final datatype will be a `blck`.

This datatype will represent a variable size block of data.
Users will have the ability to index into a `blck` as long as
no data outside of the block is being read. This will be done
by storing the size of the `blck` in the first 4 bytes of the `blck`
as a `quad`.

In practice, `blck` will be a 64-bit address.

### Procedures and The Stack
A procedure will be a `blck` with a simple structure.

```
size  : __ __ __ __     (Size of the function block)
dsize : __ __ __ __     (Size of the function's data block)
text  : __ ... __       (The function's code)
```

Upon calling a procedure an activation record is created.
This is a `blck` with the following structure.

```
size  : __ __ __ __
pc    : __ __ __ __
cblck : __ __ __ __ __ __ __ __ 
pblck : __ __ __ __ __ __ __ __ 
rreg  : __ __ __ __ __ __ __ __
data  : __ ... __
```




Procedures will be the building blocks of what the interpreter
can process. A procedure will be a self contained set of 
instructions which manipulates a constant sized `blck`. 
The `blck` will contain an areas for arguments, local variables, and
a return value. 





<!-- ### Functions and The Stack
The IR will have a very primitive notion of function
calling resembling that of assembly code. 
All arguments and local variables of a function will
reside on the stack. Arguments will be placed on the 
stack by the caller. 

We just need to make sure things like bounds checking are in place...

### Functions
A function represents the only notion of scoping 
in the IR. A function can only access its arguments
and local variables. A function can also call other
functions.

A function will have a return type (which can be void),
a list of argument types, a list of local variable
tyeps, and the function's code itself.

When a function is called, space on the stack will be 
allocated for its arguments and local variables. Then,
the functions code will start running.

### Dynamic Memory
Dynamic memory will be requestable by the program, but
also must be freed by the program. -->

