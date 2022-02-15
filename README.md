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
There will be 3 datatypes.
* `byte` 8-bit value.  (Character or Boolean)
* `quad` 32-bit value. (Signed Integer or Float)
* `array_p` 64-bit address to an `array`.
* `image_p` 64-bit address to an `image`.

These types also come with corresponding type codes for use later on. 
```
0x01 : BYTE_T
0x02 : QUAD_T
0x03 : ARRAY_P_T
0x04 : IMAGE_P_T
```

## HERE

`images` and `array` are the only 2 non primitive types in `jade`.
The can occupy arbitrarily sized blocks of memory.
The values are always accessed through the use of an `addr` pointing 
the top of the `image` or `array`.

To determine which type of value is being accessed when using an `addr`,
a constant code will always come at the top of either of any `image` or 
`array`.

```
0x01 : ARRAY_VT (VT for variable type)
0x02 : IMAGE_VT
```

All `arrays` follow the following structure....
```
0x01   : byte (ARRAY_VT code)
_ctype : quad (The type held in this array)
_len   : quad (The number of cells in this array)
val[0] : _ctype
val[1] : _ctype
   :
val[_len - 1] : _ctype
```

All `images` follow the following structure...
```
0x02     : bytes (IMAGE_VT code)
_schema  : addr (Pointer to defining schema)
field[0] : T0
field[1] : T1
   :
field[N-1] : TN-1
```


A `schema` defines the structure of a block of data known as an
`image`. A `schema` is itself an `image` which follows the following
root `schema` design.

```
_schema  : addr (Pointer to this very schema)
_scid    : quad (Schema ID, will be 5 for the root schema)
_name    : addr (The name of the schema)
_fields  : addr (Address of an array of bytes describing each field)
```
Note that these fields will not actually have their names stored.
Instead the user will index into them with a number. 
Underscores denote fields which are only readeable by the user, not writeable.



Inorder to specify types to the interpreter, here are the constant type codes.
* `0x0001` - `byte`
* `0x0002` - `quad`
* `0x0003` - `addr`
* `0x0004` - `array`
* `0x0005` - The root `schema`
* `0x....` - User defined `schema`


### Procedures
Procedures will be the by code can be made reuseable.
The low level interpreter will use static distance coordinates
for arguments and for fields in static memory.

```

```





<!-- A `schema` defines the structure of a block of data.
All blocks of data will contain a field `_schema` which points
to the block's defining structure. A block of data created 
using a `schema` is known as an `image`.

There is an `array` type for each of the 3 above primitive types.
* `byte[]`
* `quad[]`
* `addr[]`

Since arrays can have different lengths. The size of an `array` 
data block may vary depending on which `array` is being indexed.

Thus, an `array` has no constant defining `schema`. Instead,
an `array`'s structure always looks like this...

```
_schema : addr (Always NULL)
_ctype  : byte (The type stored in this array)
_length : quad (The number of elements stored in this array)
val[0]  : ctype
val[1]  : ctype
   :
val[length - 1] : ctype
```

A `schema` data block itself follows the following self 
referential `schema`.

```
_schema  : addr (Pointer to this very schema)
_name    : addr (The name of the schema)
_fields  : addr (Address of an array of bytes describing each field)
```

Each `byte` in `_fields` will give the type of a field (`byte`,
`quad`, or `addr`) and its permissions. The underscores in the examples
above denote fields which are only readable by the user. -->






<!-- The final datatype will be a `blck`.

This datatype will represent a variable size block of data.
Users will have the ability to index into a `blck` as long as
no data outside of the block is being read. This will be done
by storing the size of the `blck` in the first 4 bytes of the `blck`
as a `quad`.

In practice, `blck` will be a 64-bit address.



### Procedures and The Stack
A procedure is a `blck` will follow the following structure.
```
size  : quad (Size of this block)
name  : blck (The name of the procedure)
dsize : quad (The size of the procedure's data block)
rsize : quad (The size of the procedure's return block)
text  : blck (The code)
```

If we can index in anyway into a block... how will we be protected
from creating faulty addresses/values??
How will we know where valid values lie in a block??
Maybe involve some form of structure and array data values...
both of which are blocks??

Upon calling a procedure, an activation record will be created
to manage the call. The activation record will be a `blck` with
the following structure.

```
size  : quad (Size of this block)
pblck : blck (The called procedure block)
cblck : blck (The caller AR block)
rreg  : blck (The return register)
pc    : quad (The program counter)
dblck : blck (The data block for the AR)
rblck : blck (The return block for the AR)
``` -->


<!-- A procedure will be a `blck` with a simple structure.

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
``` -->




<!-- Procedures will be the building blocks of what the interpreter
can process. A procedure will be a self contained set of 
instructions which manipulates a constant sized `blck`. 
The `blck` will contain an areas for arguments, local variables, and
a return value.  -->





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

