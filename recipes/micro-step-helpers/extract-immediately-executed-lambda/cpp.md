{{< Staging >}}

# Extract Function

# Constraints

This recipe only works on rvalue expressions or on whole blocks (surrounded by braces) or a single for/while/if statement. If you need to extract statements but not a block, first execute the Extract Block refactoring.

# Recipe

For a block/for/while/if, surround with:
 
```cpp
[&]() {
    // ...
}();
```

For an expression, surround it with:

```cpp
[&]() { return
    // ...
;}()
```

Compile single file. Possible errors:

* `not all control paths return a value`. You have an early return. Back up and either [Eliminate Early Return/Continue/Break (fix link)](#) or extract something different.
* `a break/continue statement may only be used within ...`.  You have a break/continue. Back up and either [Eliminate Early Return/Continue/Break (fix link)](#) or extract something different. 

Search the new lambda for any return statements (using find). If there are any returns:

If it's obvious that all code paths return, then add a `return` before the lambda:

```cpp
return [&]() { 
    //...
}();
```

If it's not obvious that all code paths return, then back up and either [Eliminate Early Return/Continue/Break (fix link)](#) or try something different.

# Optional: Convert variable to capture

## Given:

```cpp
std::string s = ...

return [&]() { 
    std::cout << s;
}();
```

Add the variable to the parameter list as `auto&` and pass it as an argument

```cpp
std::string s = ...

return [&](auto& s) { 
    std::cout << s;
}(s);
```

Consider marking the parameter as `const` as a future refactoring step.
