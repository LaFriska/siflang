# SIFLANG Operators

## Simple Operators

There are several valid operators in SFLANG. The obvious ones are:

```
a + b;
a - b;
a / b;
a * b;
```

where `a` and `b` are the same type and are either `Float`, `Long` or `Int`. 

We also have bit operations.

```
a << b;
a >> b;
a | b;
a & b;
a ^ b;
~a; 
```

Then, boolean operations:

```
a && b;
a || b;
!a;
```

Here we also have the ternary operator:

```
cond ? out1 : out2
```

where `cond` is a Boolean, and `out1` and `out2` have the same types. 

## Define Operator

The `:=` operator is actually an impure operation. It creates the side effect of defining a variable within its scope. Note that in SIFLANG, variables can only be defined at most once. Naturally, `:=` returns the variable it has defined. 

## Dot Operator

Given a record type instance, use `.` to get its members. Example:

```
type Student := {
    Int uid,
    Char[] name
}

Student -> Int getUID := (s) -> s.uid;

```

## Match Operator

Use the `match` keyword with the following syntax to match alternate types.

```
match var {
    $constr1 -> return1,
    $constr2 -> return2,
    .
    .
    .
    $constr3 -> return3
}
```

For example: 

```
type Tree := $null | $tree {
    Tree left,
    Tree right,
    Int value
};

Int, Int -> Int max;
max := (a, b) -> a > b ? a : b;

Tree -> Int height;
height := (tree) -> match tree {
    $null -> 0,
    $tree -> 1 + max(height(tree.left), height(tree.right))
};

```

## Comma Operator

Commas are used very often in SIF, including in the match operator. However, the *comma operator* gives us a way to chain impure operations, in order to sequence their side effects, while returning the return value of the final impure function in the chain. The comma operator may also be used on impure SIFAPI calls such as `print`. 

```
Char[] -> Char[] id;
id := (s) -> s;

/*
Reads a line from standard in, reponds back, and return
the text.
*/
@impure(->Char[]) echo;
echo := () ->
    @print("Welcome to echo!"),
    Char[] response := @input(), //recall that := is an impure operation
    @print(response),
    id(response)
```
