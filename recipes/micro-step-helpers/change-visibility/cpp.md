{{< Concepts >}}

# Change Visibility

You have a member that you would like to change the visibility of:

```cpp
class Foo {
    Foo(const Foo&) = delete;
public:
    Foo();
}
```

Becomes:

```cpp
class Foo {
public:
    Foo();
    Foo(const Foo&) = delete;
}
```

# Recipe

## 1. Change the visibility of the member

How you do this is language dependent, but in C++ you will typically cut and paste the member into the section you need, possibly adding the section if necessary.

Q: Is there any case where this can change name resolution? Where changing some symbol between public and private changes whether it is considered for a name resolve, in such a way that it would interact with shadowing? Or do any such things result in it still resolving the same, just giving a compiler error for trying to access a private?

## 2. Build all dependent code

## 3. If you get any compile or link errors due to the new visibility revert and consider whether and how to revert this dependency.
