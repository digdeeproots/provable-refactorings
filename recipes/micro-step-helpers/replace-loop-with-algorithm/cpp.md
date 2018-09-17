{{< Concepts >}}

# replace loop with `any_of`

## 0. refactor until you have a function that looks like this:

```cpp
    for (const auto& item : collection)
    {
        if (/* some condition based on item */)
        {
            return true;
        }
    }
    return false;
```

## 1. Apply [extract-immediately-executed-lambda](../extract-immediately-executed-lambda/cpp.md) to the condition of the `if`, converting `item` from capture to parameter:

```cpp
    for (const auto& item : collection)
    {
        if ([&](const auto& item){ return /* some condition based on item */; }(item))
        {
            return true;
        }
    }
    return false;
```

## 2. [Introduce variable](...) on the lambda:

```cpp
    for (const auto& item : collection)
    {
        const auto applesauce = [&](const auto& item){ return /* some condition based on item */; };
        if (applesauce(item))
        {
            return true;
        }
    }
    return false;
```

## 3. [Move statement] up

```cpp
    const auto applesauce = [&](const auto& item){ return /* some condition based on item */; };

    for (const auto& item : collection)
    {
        if (applesauce(item))
        {
            return true;
        }
    }
    return false;
```

## 4. Replace loop with `any_of`:

    const auto applesauce = [&](const auto& item){ return /* some condition based on item */; };
    return boost::any_of(collection, applesauce);



