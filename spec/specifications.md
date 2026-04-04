# Full SIFLANG Specifications 

## Types

SIFLANG types should be conventionally written in PascalCase. 

### Primitive Types

`Int` - 32 bits. \
`Long` - 64 bits. \
`Float` - 32 bits. \
`Char` - 8 bits. \

### Object Types

Similar to Java records with immutable members, object types can be defined with the following syntax.

`type Type = {T1 n1, T2 n2, T3 n3, ..., T4 n4}`

For example, 

```
type Student := {
  Int uid,
  Char initial
};
```

### Alternate Types

Similar to Haskell, we can also alternate between subtypes to form a type. This is done using the following syntax: `type Type := $d1 T1 | $d2 T2 | ... | $dn Tn`, where `T1` to `Tn` are subtypes or nothing, and `$d1, ..., $dn` are data constructors. For example:

```
type BST := $empty | $rec {
    Int val,
    BST left,
    BST right
};
```

In each case, we can also have a single data constructor without a type to follow. 

## Functions 

Function types have an arrow `->` between the domain type and codomain type. For example, the function to calculate fibonacci numbers can be declared as follows. 

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

### Casting Types

Any primitive type can be casted to another primitive type, this is done internally by simply sign-extending, or truncating bits. C-style syntax can be used to perform the cast. For example, 

```
Long, Int -> Long mult;
mult := (l, i) -> l * (Long) i; 
```

## Expressions

Expressions are instances of types, each expression has exactly one type, there are no type-ambiguity in SIFLANG. 

### Variables 

Variables declared are expressions with their assigned type. For example, we say here that `i` has type `Int`.

```
Int a := 3;
```

### Numbers 

`Int`s, `Long`s, and `Float`s have different expressions for numbers. In this case, the number expression for `Int`s are a subset of that of `Float`s. 

For integers and longs, we accept digital notation, that is, a sequence of decimal digits. We also accept hexadecimal, which is a sequence of digits followed by `0x`, or binary notation, a sequence of binary digits followed by `0b`. 

For floats, we accept everything accepted for integers, but also we allow a decimal point specifically for the decimal notation. 

### Char Literals

A single character can be defined concretely with a character literal. For example, `Char a := 'a';`. 

### Infix Operations 

Infix operators operate on two type-equivalent sides. 