# jade

Interpreter Side Project!

## `jade` types
```
<typeID> ::= [A-Z][a-zA-Z_0-9]*

<primType> ::= 'num' | 'str' | 'bool'

<typeList> ::= ( <type> ( ',' <type> )* )?

<mapType> ::= '(' <typeList> ')' '->' <type>

<variableType> ::= <typeID> ( '{' <typeList> '}' )?

<type> ::= <primType> | <mapType> | <variableType>
```

## Defining types

```
<typeIDList> ::= ( <typeID> ( ',' <typeID> )* )?

<genericType> ::= <typeID> ( '{' <typeIDList> '}' )?

<compositeType> ::= '(' <typeList> ')'

<typeOptions> ::= <compositeType> ( '|' <compositeType> )*

<typeDef> ::= 'type' <genericType> <typeOptions>
```






