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
There will be 5 datatypes.
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
`images` and `arrays` are the only 2 non primitive types in `jade`.
The can occupy arbitrarily sized blocks of memory.
The values are always accessed through the use of an address pointing 
the top of the `image` or `array`.

__NOTE :__ underscores in the below examples denote special fields which should
never be directly accessed or modified by the user. Additonally, `R` will denote a readonly value, whereas `R+W` will denote a read and write value.

All `arrays` follow the following structure....
```
_ctype : quad (The type held in this array)
_len   : quad (The number of cells in this array)
val[0] : _ctype, R+W
val[1] : _ctype, R+W
   :
val[_len - 1] : _ctype, R+W
```

All `images` follow the following structure...
```
_schema  : image_p (Pointer to defining schema)
field[0] : T0, P0
field[1] : T1, P1
   :
field[N-1] : TN-1, PN-1
```
All `images` are constructed through the use of a `schema`. 
A `schema` itself is an `image` which defines the fields of an `image`.
Each field has a type `Ti` and a permissions tag `Pi`. All `schemas` obide by the following structure.
```
_schema  : image_p (Pointer to the root schema)
field[0] "scid"    : quad,    R (Schema ID)
field[1] "name"    : array_p, R (The name of the schema)
field[2] "fields"  : array_p, R (Address of an array of bytes describing each field)
```
The *root schema* is the `schema` which defines the fields of `schemas` themselves. (i.e. the three listed above... all of which are marked as readonly) 
The `scid` of the *root schema* is 0.

### Modules

To help with code organization, all `jade` code will belong to a `module`.
`modules` are `images` with the following structure.

```
_schema : image_p
field[0] "mid"  : quad,    R (Module ID)
field[1] "name" : array_p, R (Module name)
field[2] "procedures" : array_p, R (Module procedure pointers)
```

The `mid` 0 will be reserved for the *main module*. 
This module will contain all necessary code to start a `jade` program.

### Procedures
A `procedure` is an `image` which holds a reuseable chunk of code.
`procedures` follow the structure below.

```
_schema : image_p
field[0] "module" : image_p, R (Parent module of this procedure)
field[1] "prid"   : quad,    R (Procedure ID)
field[2] "data_schema"   : image_p, R (Schema needed for procedure data)
field[3] "text"          : array_p, R (The procedures code)
```

### Activation Records

When a procedure is called, an *Activation Record* or `ar` is created to hold all necessary information about the procedure call during its execution. An `ar` is an image with the following structure.

```
_schema : image_p
field[0] "parent_proc" : image_p, R (Procedure from which this AR was made)
field[1] "parent_ar"   : image_p, R (The AR of the calling AR)
field[2] "data"        : image_p, R (The AR's data block)
field[3] "pc"          : quad, R (The index of the next instruction to run)
```

__NOTE :__ `pc` is marked as readonly. However, its value will be changed during 
execution (just not by the user).