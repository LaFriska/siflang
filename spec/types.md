# SIFLANG Types

SIFLANG types should be conventionally written in PascalCase. 

## Primitive Types

`Int` - 32 bits. \
`Long` - 64 bits. \
`Float` - 32 bits. \
`Char` - 8 bits. \
`Bool` - 8 bits \
`Void`

### Arrays 

`T[]` is a valid array type where `T` is any type. You can get the length of the array using `array.len`.

## Custom Types

You can define custom types using `type` followed by a variable, and assignment.

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

@print(add(5)); //Prints 7.
```

Let's say we have a function `add` with type `(Int, Int) -> Int`. We can produce `addTwo` by setting `addTwo := (n) -> add(n, 2);`. More succinctly, a notational sugar for this pattern is 
```
addTwo := add(@, 2); 
```

Where the sequence of `@`'s denotes the function inputs in order. Here, `addTwo` has type `Int -> Int`.

### Casting Types

Any primitive type can be casted to another primitive type, this is done internally by simply sign-extending, or truncating bits. C-style syntax can be used to perform the cast. For example, 

```
Long, Int -> Long mult;
mult := (l, i) -> l * (Long) i; 
```

## Functions with No Inputs

Functions do not need to have any inputs. In fact, functions can have no inputs. 

```
-> Int producer;
producer = -> 23;
```
Here, `producer` is a function that produces a constant `23`.

We can also define something like this

```
Int -> Int -> Int add;
add := (a, b) -> a + b;

-> Int producer;
producer := add(2, 3);
```

This is syntactical sugar for `producer := -> add(2, 3)`, except the number of `@` is 0. 

Producers are particularly useful when chaining impure functions.
