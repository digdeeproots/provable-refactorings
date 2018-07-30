{{< Concepts >}}
{{< Unsafe reason="These are the approximate steps to do it safely, but we haven't made each be error-detecting." >}}

# Introduce Parameter Object

# Variation 1

# Constraints

* Unknown.

# Recipe

## 1. Create a new, empty class named `Applesauce`:

``` cpp
class Applesauce {
};
```

## 2. Wrap the method you are refactoring to isolate changes.

1. Overload the method under refactor with a new method. The new one has same signature and directly calls the method under refactor. This will be the wrapper method, which will enable existing callers keep working unchanged while you refactor. It will be inlined later in the recipe.

``` cpp
// Method under refactor
bool drawCircle(const int x, const int y, const int radius)
{
  // implementation
}

// Wrapper method
bool drawCircle(const int x, const int y, const int radius)
{
  return drawCircle(x, y, radius);
}
```

2. In your wrapper method, right before the call, create a new instance of your empty class and store it in a variable.

``` cpp
// Wrapper method
bool drawCircle(const int x, const int y, const int radius)
{
  Applesauce a;
  return drawCircle(x, y, radius);
}
```

3. Add new parameter of type `Applesauce` to the method under refactor, and pass in the variable.

``` cpp
// Method under refactor
bool drawCircle(const Applesauce& a, const int x, const int y, const int radius)
{
  // implementation
}
 
// Wrapper method
bool drawCircle(const int x, const int y, const int radius)
{
  Applesauce a;
  return drawCircle(a, x, y, radius);
}
```

4. Compile and check in.

## 3. Add the `public` keyword and an empty constructor to your Applesauce class.

Do not use "= default" instead of the empty braces {}, because you will be adding parameters to this constructor in the next step.

``` cpp
class Applesauce {
public:
  Applesauce()
  {}
};
```

## 4. Working in the wrapper method, add parameters to be fields on the class.

For each parameter:

1. Add the parameter received into the wrapper to the end of the constructor call for the new class.

``` cpp
// Wrapper method
bool drawCircle(const int x, const int y, const int radius)
{
  Applesauce a(x); // <-- right here
  return drawCircle(a, x, y, radius);
}
```

2. Add the argument to the constructor. Copy and paste the type signature from the wrapper method.

``` cpp
class Applesauce {
public:
  Applesauce(const int x)
  {}
};
```

3. Add a public field to the class. Paste the same type signature again. Remove any `const` or `volatile` (not sure what you should do if you have volatile params). Prepend `m_` to the variable name if that is in your style guide.

``` cpp
class Applesauce {
public:
  Applesauce(const int x)
  {}
 
  int m_x; // <-- right here
};
```

4. Initialize the field from the constructor arg.

``` cpp
class Applesauce {
public:
  Applesauce(const int x)
    : m_x(x) // <-- right here
  {}
 
  int m_x;
};
```

5. Compile and check in

## 5. Working in the method under refactor, change one argument at a time to use the value on the field.

For each argument:

1. Find every read or write for the param and replace it with a read or write for the same-named field on the new object. You can use the compiler to find them by commenting out the parameter declaration, but not any variable declaration in the function that shadows the parameter.

``` cpp
// Method under refactor
bool drawCircle(const Applesauce& a, const int x, const int y, const int radius)
{
  HorribleGraphicsAPI.drawCircle(a.x /* right here */, y, radius);
  return HorribleGraphicsAPI.HadError();
}
```

2. Compile and check in

## 6. One parameter at a time, eliminate the parameter from the method under refactor.

1. Remove unused parameter from method declaration.
2. Remove unused parameter from the method call in your wrapper method.

``` cpp
// Wrapper method
bool drawCircle(const int x, const int y, const int radius)
{
  Applesauce a(x);
  return drawCircle(a, y, radius); // <-- 'x' is no longer here
}
```

3. Compile and check in

## 7. Inline the wrapper method

1. Update one caller at a time; check in after each one.
2. Remove the wrapper method. Check in.

# Variation 2

# Constraints

* No default-valued parameters. If you have these, pass the default in at all call sites, or make an overload that doesn't have them.
* Non-constraint: You don't have to take all the parameters. Any continuous subset will do.

# Recipe

Given:

``` cpp
A F(B b, const C& c, D& d)
{
    cout << b;
}
 
 
F(b, c, d);
```

1. At the function declaration, copy the parameter list and paste it in to a new struct. replace commas with semicolons and add another semicolon at the end of the list.

``` cpp
struct F_Params
{
    B b;
    const C& c;
    D& d;
};
```

2. Change the function signature to take the params object, by value

``` cpp
F(F_Params params)
```

3. and fix up all uses of parameters to use this object:

``` cpp
{
    cout << params.b;
}
```

4. Add braces at the call sites:

``` cpp
F({b, c, d});
```

Next steps:
1. You'll probably want to follow up with removing the '&' on the fields, making copies of the parameters. Not guaranteed safe.
2. Remove const from the fields. 
