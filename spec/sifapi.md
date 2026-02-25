# SIFAPI

SIFAPI is a set of API calls syntactically in the form of functions. These functions are provided natively by the language, usually for things like I/O. This file documents all SIFAPIs. Note that SIFAPIs may not be pure, as they may produce side effects.

Note that althought types have been given to SIFAPIs here, these types are only there to guide intuition.

## SIFAPI List

```
@impure((Char[], -> Void) -> Void) @print
```

Prints a string (char array) onto the screen.

```
@impure((Char[], @impure(-> Void)) -> Void) @print
```
First, action the input `@impure(-> Void)` function, then prints the input `Char[]` onto the screen.


```
type -> type @impure(type)
```
Returns an impure version of the input type, produces no side effects. If the input type is not a function, then `@impure` is a pure identity map.



