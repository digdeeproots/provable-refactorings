{{< Concepts >}}

# Make Const

Everything possible should be marked const. Immutable data is easier to reason about and less likely to cause defects.

# Constraints

* C++ allows overloading on const, so if the variable is an object reference or pointer, making it const may cause method calls to resolve to different methods. Also, if the variable is passed to a function, the called function may change to one that expects a const argument.
* And, of course, some code may try to mutate the variable, but the compiler will catch this.
* If the variable is also the return value of the function, making it const will suppress the return-value optimization.

# Recipe

## 1: Is the type of variable a built-in type?

If so, go to step 3.

If not, perform the analysis in step 2.

## 2: Check for overloaded calls on or with the variable that may now resolve differently.

In C++, one can overload a method based on the constness of the parameter list, and one can overload member functions based on the constness of the "this" pointer. As a result, changing the constness of a variable can cause different functions to be resolved than before.

Ideally, overloaded methods have the same semantics, so this is usually safe. But nothing guarantees semantic equivalence, so this must be manually verified.

## 3: Add "const" and compile.

If the compilation succeeded, you're done.

If it failed, it's either because the value was mutated locally, or it is passed as a non-const reference to another function. In the latter case, it's possible that the other function doesn't actually need a non-const reference, and correcting the function signature is a significant benefit. However, this can result in a long chain of fixing const correctness of a large method chain.

If the other does use the varaible in a const-correct way and you want to break the chain to get this refactoring in, then perform a `const_cast<>()`, commit, then remove the cast and continue. It can be handy to create a macro for this purpose:

```cpp
#define CAST_AWAY_CONST_BECAUSE_FUNCTION_USES_AS_CONST_BUT_DOESNT_SAY_SO(type, value) const_cast< type >(value)
```
