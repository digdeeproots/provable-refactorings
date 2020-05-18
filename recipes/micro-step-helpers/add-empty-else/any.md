{{< Staging >}}

# Add empty else

It is always valid to add an empty "else". May be needed before doing the [Move statement into all branches of "if-else"](move-statement-into-all-branches-of-if-else) refactoring. Can be used to undo [Remove empty "else"](remove-empty-else).

## Constraints and Limitations

* None

## Steps

1. Add an empty "else" section after the existing "if" and "else if" sections.

Change this

```cpp
if (x == 6)
{
    ...
}
else if (y < 3)
{
    ...
}
```

to this

```cpp
if (x == 6)
{
    ...
}
else if (y < 3)
{
    ...
}
else
{
}
```
