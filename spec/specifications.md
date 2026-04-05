# Full SIFLANG Specifications 

## Types

SIFLANG types should be conventionally written in PascalCase. 

### Primitive Types

`Int` - 32 bits. \
`Long` - 64 bits. \
`Float` - 64 bits. \
`Char` - 8 bits. \
`Bool` - 8 bits. \
`Void` - 8 bits.

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

We can also alternate between subtypes to form a type. This is done using the following syntax: `type Type := $d1 T1 | $d2 T2 | ... | $dn Tn`, where `T1` to `Tn` are subtypes or nothing, and `$d1, ..., $dn` are data constructors. For example:

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

Note that functions can have zero or more inputs and zero or more outputs. When this is the case, `Void` is implicitly assigned to its domain or codomain. `Void` has exactly one instance, which is `null`. For example, here is a function with no return value.

```
(Int ->) consumer; // The parentheses are optional
```

This is exactly equivalent to 

```
Int -> Void consumer;
```

Here is a function with no input.

```
(-> Int) producer;
```

We can also have functions with no input or outputs. 

```
(->) doNothing;
```

Again, this is equivalent to 

```
Void -> Void doNothing;
```

Functions with no inputs are known as producers, functions with no outputs are known as consumers, and functions without both are called null maps. In a purely functional setting, it may seem strange why we should allow this. However, when functions have side effects, i.e. when being marked as `@impure`, these functions can be very helpful. 

### Casting Types

Any primitive type can be casted to another primitive type, this is done internally by simply sign-extending, or truncating bits. C-style syntax can be used to perform the cast. For example, 

```
Long, Int -> Long mult;
mult := (l, i) => l * (Long) i; 
```

- Casting an `Int a` to `Long l` results in the two's complement representation of `a` being sign-extended to 64-bits to `l`.
- Casting a `Long l` to `Int a` is potentially unsafe. If the value `l` represents can fit in the 32-bit integer range, then it should be converted to that integer. Otherwise, undefined behaviour. 
- Casting an `Int` and `Long` to a float is undefined behaviour (this specification leaves it undefined for compiler/interpreter writers to find their own way to make this conversion.)
- Casting a `Float` to `Int` and `Long` is undefined behaviour. 
- Casting `Char c` to `Int i` or `Long l` is done by returning `c`
's ASCII representation.
- Casting from `Void` and `Bool` is undefined.

## Expressions

Expressions are instances of types, each expression has exactly one type, there are no type-ambiguity in SIFLANG. 

### Void Expressions

The only void expression is `null` and any function expressions with return type `null`.

### Variables 

Variables declared are expressions with their assigned type. For example, we say here that `i` has type `Int`.

```
Int a := 3;
```

### Numbers 

`Int`s, `Long`s, and `Float`s have different expressions for numbers. In this case, the number expression for `Int`s are a subset of that of `Float`s. 

For integers and longs, we accept digital notation, that is, a sequence of decimal digits. We also accept hexadecimal, which is a sequence of digits followed by `0x`, or binary notation, a sequence of binary digits followed by `0b`.

For floats, we accept everything accepted for integers, but also we allow a decimal point specifically for the decimal notation. 

Any number expression too large for its assigned type is still assignable, although its behaviour is undefined.

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

To define an object, simply write the subexpressions within the curly braces separated by commas. 

For example,
```
type Student := {
  Int uid,
  Char initial
};

Student joe := {
  7000 + 300,
  'J'
};
```

To get a field of an object, use the **dot operator**.

```
@put(joe.initial); 
```

### Alternation Expressions

To construct an alternate type, follow a data constructor `$dat` with the constructor of its subtype, or leave it empty if there is no types to follow. 

Refer to the following list type.

```
type List := $emp | $rec {Int val, List sublist};
```

We can construct a list containing integers `[1, 2, 3]` as follows.

```
List list := $rec {1, $rec {2, $rec {3, $emp}}};
```

### Function Expressions 

A function expression is an instance of a function type. It takes a possibly empty list of arguments in parentheses, with a `=>` to an expression output.

For example, 
```
(Int a, Int b) => a + b
```
corresponds to the function of adding two integers together.

When assigning a function expression to a variable declared with a function type, we can omit the types of formal parameters of the function parameters.

For example, we can rewrite the following

```
(Int, Int) -> Int add;
add := (Int a, Int b) => a + b;
```

with the following.

```
(Int, Int) -> Int add;
add := (a, b) => a + b;
```

This is because we are able to infer the types of `a` and `b` in compile. 

An impure function expression can be determined at compile time, it is an impure function only if it makes an impure call. 

Example:

```
() => @put('H'), @put('i'); 
```


### Applying Functions

Applying functions has C-style syntaxes. 

```
Int -> Int addTwo;
addTwo := (n) => n + 2;

Int -> Int addFour;
addFour := (n) => addTwo(addTwo(n));
```

#### Notational sugar for voids 

When returning a `Void` type, we require our function expression to specify the return type as `null`. This is to enforce better code readability. 

Example:

```
Char -> consumeChar := (c) => null;
```

However, when accepting a `Void` type, we allow leaving it empty as notational sugar.

For example, the following two functions are equivalent.

```
(->) doNothing1 := (null) => null;
(->) doNothing2 := () => null;
```

### Match Expressions 

We can switch over an instance of an alternate type using match expressions. 

```

type String := $emp 
             | $rec {Char x; String xs}; 

@impure(String ->) print;
print := (s) => ?s {
  $emp   : null;
  $rec o : @put(o.x), print(o.xs);
};

```

Here, we use the syntax 
```
?thingToSwitch {
  $alt1 t1 : ret ;
  $alt2 t2 : ret ;
  .
  .
  .
};
```

### Operators 

The following lists the operators for the above expressions from highest to lowest order of precedence.
1. Functional Call Operators
2. Dot Operator
3. Prefix Operators
4. Multiplication, Division, Modulo, and Integer Division
5. Addition, Subtraction
6. Bitwise Operators
7. Comparison Operators
8. Equivalence Operators
9. Boolean Operators
10. Conditional Operators
11. Match Operators
12. Comma Operators
13. Function Operators (`=>`)

## Impure Operations and Comma Expressions

Impure functions are functions with side effects. In SIFLANG, side effects mainly include I/O operations. Side effects also include things like defining a variable within its scope.

For example, printing a string onto standard out, and taking an input from standard in are impure operations.
Impure functions may call any type of functions, but pure functions may never call impure functions. Note also
that SIFLANG's side effects are not meant for programmers to write imperative code, which means mutable variables
do not exist in SIFLANG.

### Chaining Impure Operations

Sometimes, we want to call several impure functions just for their side effects. This is done using the `,` expression, we can chain several impure functions together. The return value of this expression is that of the final impure function in the chain. For example, 

```
@impure((Char, Int) -> Int) printAndRet;
printAndRet := (c, i) => @put(c), i;
```

Consider the following example.

```
@impure(->) printHi;
printHi := () => @put('H'), @put('i'), @put('!');
```

Here, the function type requires a `Void` instance, which is always `null`, to be returned. Since `@put` calls have type `Char -> Void`, the expression `@put('!')` evaluates to `null`. 

## SIFAPI

SIFAPI is a set of natively supported helper calls used to do things pure functions cannot achieve. These calls solves a range of problems, such as handling side effects (I/O). In general, SIFAPIs can be used as normal functions, using the function call operator. SIFAPI calls are those that begin with a `@`, programmers are not allowed to define their own SIFAPIs. This section documents all SIFAPIs. 

### SIFAPI List

Some SIFAPIs have a specific function type, and some do not. Those that do are *functional* APIs.

The `@put` functional API has type `@impure(Char ->)`. It prints a character `c` to standard out, returning nothing.

Example usage:

```
@put('A'); // Prints character 'A'
```

The `@impure` API is not function. It takes a function type as input, and returns its impure version. 

Example usage:

```
@impure(String ->) println;
```

Example usage:
```
@impure(Char ->) putTwise;
main := (c) => @put(c), @put(c), @put('\n');
```

The `@get` functional API has type `@impure(-> Char)`, it reads a single character from standard in. If nothing is in standard in, it hangs.

Example usage:
```
@impure(->) charEcho;
charEcho := () => @put(@get());
```

The `@string` API is the only function in the language that can take 
in a string lateral. In particular, it takes a string lateral, and
returns a `type String := @emp | @rec {Char c, String cs}` type. Note that type-equivalence is structural in SIFLANG, so users may define their own type to store the result of `@string` as long as the structure is identical. 

Example usage:
```
type String := @emp | @rec {Char c, String cs};
String hello = @string("Hello");
```