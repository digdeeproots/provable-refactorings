{{< Concepts >}}

# Convert Free Function to Static

# Recipe

## 1. Apply Move Method

[Move Method (fix link)](#) to the same .cpp file (compilation unit) as the class.

## 2. Make the function a static member

a. If the function is in an anonymous namespace, remove it from the anonymous namespace and compile.

b. Copy + paste the function signature into the public section of the class definition in the class's header file.

``` cpp
class Cls
{
public:
  void F(const Cls& cls)
}
```

c. Insert the keyword static in front of the signature you pasted, and put a semicolon at the end.

``` cpp
class Cls
{
public:
  static void F(const Cls& cls);
}
```

d. In the .cpp implementation file, add the class's name plus the scope resolution operator "::" to the front of the function's name.

``` cpp
void Cls::F(const Cls& cls)
{
  cls.blah();
}
```

e. For each caller of this method, add the class's name plus the scope resolution operator "::" to the front of the function's name.

f. Compile