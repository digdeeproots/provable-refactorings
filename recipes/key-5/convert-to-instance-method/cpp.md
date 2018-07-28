{{< Draft >}}

# Constraints

The method has a parameter that refers to the object that the method will become an instance of. Example:

``` cpp
// Const parameter
void F(const Cls& cls)
{
}
 
 
// will eventually become:
class Cls
{
  void F() const; // <-- notice the const on the end
}
 
// Non-const parameter
void F(Cls& cls)
{
}
 
// will eventually become:
class Cls
{
  void F(); // <-- notice there is NO const on the end
}
```

# Recipe

## 1. Convert Free Function to Static Method

Apply the [Convert Free Function to Static Method (fix link)](#) refactoring to:
* move the function to the compilation unit (.cpp file) containing the class implementation.
* make the function a static method of the class.

## 2. Make function an instance method

### a. In the header file, convert the static to an inline delegate to a new instance method.

``` cpp
class Cls
{
public:
  static void F(const Cls& cls);
}
```

becomes

``` cpp
class Cls
{
public:
  static void F(const Cls& cls) { cls.F(cls); }
  void F(const Cls& cls);
}
```

Do this with copy and paste, not by re-typing.

### b. If the class instance parameter is const, put a const at the end of the instance method's signature in BOTH the header and implementation files:

``` cpp
// Header file
class Cls
{
public:
  static void F(const Cls& cls) { cls.F(cls); }
  void F(const Cls& cls) const; // <-- add const at the end
}
 
 
// Implementation file
void Cls::F(const Cls& cls) const // <-- add const at the end
{
  cls.blah();
}
```

### c. Update each caller of this function to use it as an instance method

Note: you can find callers by commenting out the static method, compiling, and looking for compiler errors.

Do a few instances, then uncomment the static method, compile, and commit. Repeat until done (or bored).

1. Find the argument that corresponds to the class parameter.
2. If it is not a simple variable name, then perform an Extract Variable refactoring to create a variable name.
3. Copy the variable name.
4. Highlight the class's name plus the scope resolution operator that is in front of the function's name.
5. Paste the variable name in front of the function's name, followed by a dot, to create an instance method call.

If you updated all callers, then remove the static inline function.

## 3. Replace references to class with (*this)

### a. In the function body, replace references to the class instance with (*this).

``` cpp
void Cls::F(const Cls& cls)
{
  (*this).blah(); // <-- replace 'cls' with (*this)
}
```

### b. Compile & commit

## 4. Remove unused instance parameter

1. In the class's header file, remove the class parameter from the function's signature.
2. Do the same in the function's implementation.
3. Compile. For each error:
  1. Remove the class parameter.
4. Compile & Commit
