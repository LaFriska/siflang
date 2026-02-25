# SFLANG Types

SFLANG types should be conventionally written in PascalCase. 

## Primitive Types

`Int` - 32 bits. \
`Long` - 64 bits. \
`Float` - 32 bits. \
`Char` - 8 bits. \
`Bool` - 8 bits \
`Type`

### Arrays 

`T[]` is a valid type where `T` is a type. Arrays have as `len` member, which can be accessed through `array.len`, access array elements through `array[i]` where `i` has type `Int`. 
 
## Custom Types

You can define custom types using `type` followed by a variable, and assignment.

### Singleton Types

A singleton type can be defined as just a single keyword. For example,

```
type Nothing := $nothing;
```

Here, `$nothing` is a data constructor for the type `Nothing`. Every data constructor has a `$` before it.

### Object Types

Similar to Java records with immutable members, object types can be defined with the following syntax.

`type Type = {T1 n1, T2 n2, T3 n3, ..., T4 n4}`

For example, 

### Alternate Types

Similar to Haskell, we can also alternate between subtypes to form a type. This is done using the following syntax: `type Type := T1 | T2 | ... | Tn`, where `T1` to `Tn` are subtypes. For example:

```
type BST := $emptyTree | {
    Int val,
    BST left,
    BST right
};
```

Note that data constructors are written in camelCase.

## Functions 

Functions also have types. This is identified by having an arrow `->` between the domain type and codomain type. For example, the function to calculate fibonacci numbers can be declared as follows. 
``` 
Int -> Int fib;
```

`->` by default is right-way associative, like Haskell, so we can use currying to create functions with several inputs.

```
Int -> Float -> Bool -> Char weirdFunction;
```

This is equivalent to

```
Int -> (Float -> (Bool -> Char)) weirdFunction;
```

Functions with multiple inputs can also have their inputs separated out using commas. For example, we could have

```
(Int, Int, Int) -> Bool areEqual;
```

Although semantically, this is usually equal to the curried version:

```
Int -> Int -> Int -> Bool areEqual;
```

These two types **are not equal**. 

### Applying Functions

Applying functions has C-style syntaxes. 

```
Int -> Int addTwo;
add := (n) -> n + 2;

print add(5); //Prints 7.
```

Let's say we have a function `add` with type `(Int, Int) -> Int`. We can produce `addTwo` by setting `addTwo := (n) -> add(n, 2);`. More succinctly, a notational sugar for this pattern is 
```
addTwo := add(@, 2); 
```

Where the sequence of `@`'s denotes the function inputs in order. Here, `addTwo` has type `Int -> Int`.

