{{< Draft >}}

# Split String


You have a function you would like to add a return at the end of:

```cpp
"This string has 2 parts"
```

Becomes:

```cpp
"This string has "+"2 parts"
```

# Recipe

## 1. Add std::string() to beginning if needed

Copy/paste the following before the string
```cpp
std::string() +
```

The code should now look like:
```cpp
std::string() + "This string has 2 parts"
```

Notes: this step might not be needed if you are already have a std::string object being concatenated to the string already.
You can test this by skipping this step.

**Proof:** The code should **compile**

Notes: the only other way the code will compile after pasting this is if you paste into a comment or between an existing string
```cpp
"std::string() + sometext";
```

## 2. Add Seperator
Now copy/paste the following into the middle of the string where you want to split it
```cpp
"+"
```

**Proof:** The code should  **compile**
