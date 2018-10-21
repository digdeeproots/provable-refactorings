{{< Concepts >}}

Most loops that compute a result, but don't have an external side effect, can be expressed as an algorithm.

# replace loop with `any_of`

## Before:

```cpp
for (const auto& item : collection)
{
    if (predicate(item))
    {
        return true;
    }
}
return false;
```

## After:

```
return boost::any_of(collection, predicate);
```

# replace loop with `transformed`

## Before:

```cpp
for (const auto& item : collection)
{
    result.push_back(bar(item));
}
```

## After:

```
boost::copy(collection | boost::adaptors::transformed(bar), std::back_inserter(result));
```
