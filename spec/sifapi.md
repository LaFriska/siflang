# SIFAPI

SIFAPI is a set of natively supported helper calls used to do things pure functions cannot achieve. These calls solves a range of problems, from retrieving the length of an array in constant time, to handling side effects (I/O). This file documents all SIFAPIs. Note that SIFAPIs may not be pure, as they may produce side effects.

<!-- Note that althought types have been given to SIFAPIs here, these types are only there to guide intuition. SIFAPIs are not technically functions, so having input types and return types do not technically make sense. -->

## SIFAPI List

Like functions, calls can either be pure or impure, which means some calls can be called in all contexts, but some cannot. 

```
@print(s)
```

Prints a string `s` to standard out, returning nothing. `@print` is impure. 

```
@input()
```

Gets a line of text from standard in, returns the text, this call is impure.

```
@impure(funcType)
```

With a given function type as an input, returns the same function except marked internally as impure. This call is ironically pure. 

Example usage:
```
@impure(Char[] ->) printToString;
main := (s) -> @print("Your inputs are: "), @print(s);
```

This function prints the input string. Note that the comma is an operator used to chain impure functions.

```
@len(arr)
```

Returns the length of an array as an integer. 


