{{< Concepts >}}

# Inline Function

This recipe is essentially a reverse of [Extract Function (fix link)](#).

# Constraints

You can't inline if it's polymorphic.

#Recipe

1. For member functions, move the definition in to the class declaration
2. If overloaded, rename to eliminate the overload
3. Convert the function to use ->ret syntax for its return type
4. If the caller uses the return value
  a. Introduce Variable on the return value
  b. Split declaration and initialization
  c. Surround the call with a block.
5. Copy the function definition. Paste immediately above the call. Add `= [this]` or `= []` after the name, and a semicolon at the end.
6. If the lambda returns a value,
  a. Add the result variable to the capture list, by reference (`[this, &result]`)
  b. Replace each `return blah;` statement in the lambda with `result = blah; return result;`
  c. Change the lambda return type to `void`
7. If there is an early return, eliminate it (only return should be the last statement)
8. Eliminate parameters
  * Option 1: capture calling arguments by reference + move parameters to locals and initialize with those arguments, renaming if they are the same
  * Option 2: move by-ref parameters/arguments to capture by ref, renaming if they are different
9. Delete the lambda declaration & the call and compile.

For example:

``` cpp
auto Foo() -> void { blah(); };
Foo();
```

Becomes:

``` cpp
blah();
```

10. Compile
