{{< Concepts >}}

# Extract Parameter

Transforms a local variable or expression into a parameter (if expression, it pushes evaluation of that expression to all callers).

# Variation 1

1. Apply refactoring: [Introduce Variable (fix link)](#)
2. Apply refactoring: Move variable to top of function (Reorder Independent Statements)
3. [Extract Function (fix link)](#) on the rest of the function, all but the variable
4. [Inline Function (fix link)](#) --- you inline the old function (not the new one)

# Variation 2

1. Apply refactoring: [Introduce Variable (fix link)](#)
2. Apply refactoring: Move variable to top of function (Reorder Independent Statements)
3. Put the variable in the parameter list, and the expression at the call site (this will only work if the variable names and types are the same). (This is really several steps.)
