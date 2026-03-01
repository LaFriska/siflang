# SIFLANG Standard Library

Unlike many other languages, SIFLang standard library is imported without having to use `import` or other keywords. Since this language is very bare-bones and for educational purposes only, it does not support importing different modules (yet).

Nonetheless, the standard library provides some important functionalities. 

## Lists

Like Haskell, lists are used to store multiple elements of the same type. Since this language does not support ad hoc polymorphism, there will be a list of each primitive type in the standard library. 

For example:

```
type CharList := $emp
               | $rec {Char char, CharList sublist};

type IntList := $emp
              | $rec {Int int, IntList sublist};
```

We also have the alias:

```
type String := CharList;
```

