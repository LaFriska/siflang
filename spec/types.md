# SIFLANG Types

SIFLANG types should be conventionally written in PascalCase. 

## Primitive Types

`Int` - 32 bits. \
`Long` - 64 bits. \
`Float` - 32 bits. \
`Char` - 8 bits. \
`Bool` - 8 bits \

### Singleton Types

A singleton type can be defined as just a single data constructor. For example,

```
type Nothing := $nothing;
```

Here, `$nothing` is a data constructor for the type `Nothing`. Every data constructor has a `$` before it.

### Object Types

Similar to Java records with immutable members, object types can be defined with the following syntax.

`type Type = {T1 n1, T2 n2, T3 n3, ..., T4 n4}`

For example, 

```
type Student := {
  Int uid,
  Char[] name
}
```

### Alternate Types

Similar to Haskell, we can also alternate between subtypes to form a type. This is done using the following syntax: `type Type := $d1 T1 | $d2 T2 | ... | $dn Tn`, where `T1` to `Tn` are subtypes, and `$d1, ..., $dn` are data constructors. For example:

```
type BST := $empty | $rec {
    Int val,
    BST left,
    BST right
};
```

In each case, we can also have a single data constructor. 

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

### Functions with no inputs or outputs

Note that functions can have zero or more inputs and zero or more outputs. Simply leave the parameter list or return type empty. For example, here is a function with no return value.

```
Int -> consumer;
```

Here is a function with no input.

```
-> Int producer;
```

We can also have functions with no input or outputs. 

```
-> doNothing;
```

Functions with no inputs are known as producers, functions with no outputs are known as consumers, and functions without both are called trivial functions. In a purely functional setting, it may seem strange why we should allow this. However, when functions have side effects, i.e. when being marked as `@impure`, these functions can be very helpful. 

### Applying Functions

Applying functions has C-style syntaxes. 

```
Int -> Int addTwo;
addTwo := (n) -> n + 2;

Int -> Int addFour;
addFour := (n) -> addTwo(addTwo(n));
```

Let's say we have a function `add` with type `(Int, Int) -> Int`. We can produce `addTwo` by setting `addTwo := (n) -> add(n, 2);`. More succinctly, a notational sugar for this pattern is 
```
addTwo := add(., 2); 
```

Where the sequence of `.`'s denotes the function inputs in order. Here, `addTwo` has type `Int -> Int`.

### Casting Types

Any primitive type can be casted to another primitive type, this is done internally by simply sign-extending, or truncating bits. C-style syntax can be used to perform the cast. For example, 

```
Long, Int -> Long mult;
mult := (l, i) -> l * (Long) i; 
```