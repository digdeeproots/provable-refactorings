{{< Concepts >}}

# Make Method Static

# Constraints

* There shall not be calls to instance methods. Convert them to static.
* The method shall be private. (Makes introduce parameter easier. TODO: remove this constraint)

# Recipe

## 1. For each reference to an instance variable, apply Introduce Parameter.

For example,

``` cpp
class Foo {
    void Bar() const;
    int m_width;
};
 
void Foo::Bar() const
{
    cout << m_width;
}
 
foo.Bar();
pFoo->Bar();
```

Becomes:

``` cpp
class Foo {
    void Bar(int width) const;
    int m_width;
};
 
 
void Foo::Bar(int width) const
{
    cout << width;
}
 
foo.Bar(m_width);
pFoo->Bar(m_width);
```

## 2. Find All References

This is hard. VisualAssist sometimes gets it wrong.

## 3. Remove const, add static, fix all callers, replacing member access operators . and -> with the scope resolution operator.

Should this be multiple steps? How can we do this incrementally, in case there are lots of callers? Probably need to add a set-up step where we create a delegating thunk for a while, which we will clean up later.

``` cpp
class Foo {
    static void Bar(int width);
    int m_width;
};
 
void Foo::Bar(int width)
{
    cout << width;
}
 
Foo::Bar(m_width);
Foo::Bar(m_width);
```

## 4. Change it to a free function.

Provided that the static function does not use any private features of the original class, change it to a free function.  Move the declaration from the class to just after the class, remove static.  At the definition remove the scope.  Remove the scope resolution operators at each call site.

``` cpp
class Foo {
};
void Bar(int width);
 
void Bar(int width)
{
    cout << width;
}
 
 
Bar(m_width);
Bar(m_width);
```

## 5. Move the free function to a namespace

Consider whether the free function should be in some namespace and move it there if so.  Note: Generally the function should be in the same namespace as the types it operates on, which is often the global namespace. Some other times a local, anonymous namespace is a good idea.
