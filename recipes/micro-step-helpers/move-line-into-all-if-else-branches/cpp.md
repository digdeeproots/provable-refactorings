{{< Needed >}}

# Move line into all if/else branches

Duplicate a line outside an if/else structure to be included:

```cpp
if(a)
{
  // somecode
}
else if (b)
{
  // somecode  
}
else
{
  // somecode
}
// last line
```

Becomes:

```cpp
if(a)
{
  // somecode
  // last line
}
else if (b)
{
  // somecode  
  // last line
}
else
{
  // somecode
  // last line
}
```
