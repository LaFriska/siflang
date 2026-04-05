# Full SIFLANG Specifications 

## Types

SIFLANG types should be conventionally written in PascalCase. 

### Primitive Types

`Int` - 32 bits. \
`Long` - 64 bits. \
`Float` - 32 bits. \
`Char` - 8 bits. \
`Bool` - 8 bits

### Object Types

Similar to Java records with immutable members, object types can be defined with the following syntax.

`type Type = {T1 n1; T2 n2; T3 n3; ...; T4 n4}`

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
addTwo := (n) => n + 2;

Int -> Int addFour;
addFour := (n) => addTwo(addTwo(n));
```

### Casting Types

Any primitive type can be casted to another primitive type, this is done internally by simply sign-extending, or truncating bits. C-style syntax can be used to perform the cast. For example, 

```
Long, Int -> Long mult;
mult := (l, i) => l * (Long) i; 
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

### Booleans 

A boolean expression is either `true` or `false`.  

### Infix Operations 

Infix operators operate on two type-equivalent sides. 

- `+` and `-` corresponds to addition and subtraction, it accepts all primitive types. 
- `*` corresponds to multiplication, it accepts integers, longs and floats. 
- `//` corresponds to integer division, it accepts integers and longs.
- `/` corresonds to floating point division, it accepts only floats. 
- `%` corresponds to modulo arithmetic (not remainder operation), accepting only integer. 
- Bitwise operations `&`, `|` and `^` takes only integer and longs.
- Boolean operators `&&`, `||` applies only to bools. 
- Equivalence operators `=` and `!=` can be applied to any type. The expression has type `Bool`.
- Comparison operators `<=`, `>=`, `<`, `>` can only be applied to ints, longs and floats. The expression has type `Bool`.

### Prefix Operations

- `-` takes the negative of a number type. It accepts ints, floats and longs. 
- `!` is the not of a boolean expression. 

### Conditional Expression 

There's no if statements in SIFLANG, but there is conditional expressions instead. They have the following form:

```
boolExpr ? trueExpr : falseExpr
```

where the type of `trueExpr` and `falseExpr` are equal, and the type of `boolExpr` is `Bool`. 

### Object Expressions

To define an object, simply write the subexpressions within the curly braces separated by semicolons. 

For example,
```
type Student := {
  Int uid,
  Char initial
};

Student joe := {
  7000 + 300; 
  'J'
};
```

To get a field of an object, use the **dot expression**.

```
@put(joe.initial); 
```

### Function Expressions 

A function expression is an instance of a function type. It takes a possibly empty list of arguments in parentheses, with a `=>` to an expression output.

For example, 
```
(Int a, Int b) => a + b;
```
corresponds to the function of adding two integers together.

An impure function expression can be determined at compile time, it is an impure function only if it makes an impure call. 

```
() => @put('H'), @put('i'); 
```

### Match Expressions 

We can switch over an instance of an alternate type using match expressions. 

```

type String := $emp 
             | $rec {Char x; String xs}; 

@impure(String ->) print;
print := (s) => ?s {
  $emp   : @trivial();
  $rec o : @put(o.x), print(o.xs);
}

```

Here, we use the syntax 
```
?thingToSwitch {
  $alt1 t1 : ret ;
  $alt2 t2 : ret ;
  .
  .
  .
}
```

### Comma Expressions 

See the Impure Operations section.

## Impure Operations 

Impure functions are functions with side effects. In SIFLANG, side effects mainly include I/O operations. Side effects also include things like defining a variable within its scope.

For example, printing a string onto standard out, and taking an input from standard in are impure operations.
Impure functions may call any type of functions, but pure functions may never call impure functions. Note also
that SIFLANG's side effects are not meant for programmers to write imperative code, which means mutable variables
do not exist in SIFLANG.

### Chaining Impure Operations

Sometimes, we want to call several impure functions just for their side effects. This is done using the `,` expression, we can chain several impure functions together. The return value of this expression is that of the final impure function in the chain. For example, 

```
@impure((Char, Int) -> Int) printAndRet;
printAndRet := (c, i) => @put(c), @trivial(i)
```

## SIFAPI

SIFAPI is a set of natively supported helper calls used to do things pure functions cannot achieve. These calls solves a range of problems, from retrieving the length of an array in constant time, to handling side effects (I/O). This file documents all SIFAPIs. Note that SIFAPIs may not be pure, as they may produce side effects.

<!-- Note that althought types have been given to SIFAPIs here, these types are only there to guide intuition. SIFAPIs are not technically functions, so having input types and return types do not technically make sense. -->

### SIFAPI List

Like functions, calls can either be pure or impure, which means some calls can be called in all contexts, but some cannot. 

```
@put(c)
```

Prints a character `c` to standard out, returning nothing. This call is impure. 

```
@impure(funcType)
```

With a given function type as an input, returns the same function except marked internally as impure. This call returns the function itself if it is already impure. 

Example usage:
```
@impure(Char ->) putTwise;
main := (c) => @put(c), @put(c), @put('\n');
```

This function prints the input string. Note that the comma is an operator used to chain impure functions.

`@trivial()` returns a trivial function that takes any arguments and returns itself. It can also take zero arguments. This can be usedful in chaining impure operations to supply a return value. 
