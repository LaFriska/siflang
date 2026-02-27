# Impure Functions

Impure functions are functions with side effects. In SIFLANG, side effects mainly include I/O operations. Side effects also include things like defining a variable within its scope.

For example, printing a string onto standard out, and taking an input from standard in are impure operations.
Impure functions may call any type of functions, but pure functions may never call impure functions. Note also
that SIFLANG's side effects are not meant for programmers to write imperative code, which means mutable variables
do not exist in SIFLANG.

## Declaring an Impure Function

To declare an impure function, pass the function type through the `@impure` sifapi call. For example:

```
@impure(Char[] ->) printStr;
printStr := string ->; 
```
Here, the main function takes a string as an input, does nothing, and returns nothing.

## Print to Standard Out

Use the `@print` sifapi to print something to standard out, and `@input` for reading from standard in.

For example,

```
@impure(Char[] ->) printStr;
printStr := string -> @print(string);
```

## The Purity Rule

The purity rule states that impure operations may call pure and impure operations, but pure operations may only call pure operations. It is also generally bad practice to mark a function impure, when it is actually pure. For example, please don't do this

```

@impure((Int -> Int) -> Int[]) applyMap;
applyMap := 

```