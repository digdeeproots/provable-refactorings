{{< Concepts >}}

# Change () or {} initialization to = initialization

Too many braces make code harder to read

For example:

```cpp
const AxisHeaderIndices& xIndices(model.GetXAxis()->GetHeaderIndices(paneDesc.GetXFields(), paneDesc.GetXIndex()));
```

Becomes:

```cpp
const AxisHeaderIndices& xIndices = model.GetXAxis()->GetHeaderIndices(paneDesc.GetXFields(), paneDesc.GetXIndex());
```

# Constraints

{} initialization has more strict rules so be sure you have the right type or that it doesn't matter.

# Recipe

a. add = after the variable.
b. Remove the parentheses or curly braces from the rest of the line.
c. Compile
