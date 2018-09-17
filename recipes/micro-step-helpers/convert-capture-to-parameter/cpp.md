{{< Staging >}}

# Recipe

## Given:

A lambda which captures by reference:

```cpp
std::string s = ...

return [&]() { 
    std::cout << s;
}();
```

Add the variable to the parameter list as `auto&` and pass it as an argument, using the same name in both places.

```cpp
std::string s = ...

return [&](auto& s) { 
    std::cout << s;
}(s);
```

Consider marking the parameter as `const` as a future refactoring step.
