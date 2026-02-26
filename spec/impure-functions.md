# Impure Functions

Impure functions are functions with side effects. In SIFLANG, side effects mainly include I/O operations.
For example, printing a string onto standard out, and taking an input from standard in are impure operations.
Impure functions may call any type of functions, but pure functions may never call impure functions. Note also
that SIFLANG's side effects are not meant for programmers to write imperative code, which means mutable variables
do not exist in SIFLANG.

## Declaring an Impure Function

To declare an impure function, pass the function type through the `@impure` sifapi call. The best example 
is the main function:

```
@impure(Char[] ->) main;
main := string ->; 
```
Here, the main function takes a string as an input, does nothing, and returns nothing.

## Print to Standard Out

Use the `@print` sifapi to print something to standard out, and `@input` for reading from standard in.

For example,

```
@impure(Char[] ->) main;
main := string -> @print(string);
```

## Chaining Impure Functions

Impure functions can be chained, in order to apply their side effects sequentially. This is done using the `,` operator.
For example,

```
@impure(Char[] ->) main;
main := string -> @print("Your inputs are: "), @print(string), @print("\n");
```
