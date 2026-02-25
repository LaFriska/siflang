# SFLANG Operators

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

## then

`then` is an operator on `@impure` functions. In particular, suppose we have a function `func` with the following type.

```
@impure(T1, T2, T3, ..., @impure(-> Void)) 
```
In particular, we have an impure function that takes nothing as input, and produces nothing. If we consider the last parameter as some side effects to execute before `func`'s side effects are executed, then we can simulate imperative side effect code by chaining multiple function calls.

However, this is syntactically disgusting. Thus, we define a syntactical sugar for this pattern. Instead of
```
func(t1, t2, t3, ..., anotherFunc);
```

we can simply call
```
anotherFunc then func(t1, t2, t3, ...)
```


