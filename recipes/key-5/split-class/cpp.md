{{< Draft >}}

# Split Class

This recipe is designed Extract one responsibility from one class and nothing else. If you want to do something else, consult the other refactorings.

# Constraints

* The methods that you want to extract do not depend on any methods or fields that you do not want to extract. You may need to apply other refactorings first to break such dependencies. Alternatively, you could create the `OneResponsibility` class with a back-reference to the `Original` class and then use other refactorings to clean things up after you finish the extraction.
* The methods and fields that you want to extract are all `private`. If they are `public`, then:
  * For `public` methods: first use [Extract Method (fix link)](#) on the entire method body to extract out a `private` method to move.
  * For `public` fields: first introduce accessor methods (needs a recipe) and make the field `private`.

# Recipe

## 1. Initial state

We want to extract class `OneResponsibility` from class `Original`. We want to move method `MovingMethod` and field `m_movingField` to the extracted class.

Original.h:

```cpp
class Original
{
public:
    void StayingMethod();

private:
    void MovingMethod();

    int m_movingField;
};
```

Original.cpp:

```cpp
void Original::StayingMethod()
{
    MovingMethod();
}
  
void Original::MovingMethod()
{
    m_movingField = 42;
}
```

## 2. Declare and instantiate the empty `OneResponsibility` class

Above the `Original` declaration, add a minimal declaration of the new `OneResponsibility` class and instantiate it as a field in the `Original` class. Use a `friend` declaration to allow the `Original` class to access private members of the `OneResponsibility` class.

In Original.h:

```cpp
class OneResponsibility
{
    friend class Original;

public:
private:
};

class Original
{
public:
    void StayingMethod();

private:
    void MovingMethod();

    OneResponsibility m_helper;
    int m_movingField;
};
```

Compile single file. Commit.

## 3. Move fields to the `OneResponsibility` class

If it isn't obvious which fields you need to move, quick-move a method (no need to be safe) and let the compilation errors tell you which fields to move. Then revert. Then move those fields.

Move one field at a time, as follows.

### a. Ensure the field name is unique in the class files

Do a search for the field name. Check each reference and ensure than none of them are variable declarations or parameter declarations. If there are such declarations, then first [Rename Variable (fix link)](#) to separate them. This will allow the compiler to detect errors later.

### b. Cut the field from the Original class and paste it into the `OneResponsibility` class

In Original.h:

```cpp
class OneResponsibility
{
// ...
private:
    int m_movingField;
};

// ... Original no longer contains m_movingField ...
```

### c. In the Original class methods, add `m_helper.` to each reference to the field.

Compile single file will find all the references you have to update.

In Original.cpp:

```cpp
void Original::MovingMethod()
{
    m_helper.m_movingField = 42;
}
```

Compile single file to make sure you got them all.

### d. Move initialization.

If the field is being initialized in the `Original` class constructor, move the initialization to the `OneResponsibility` class constructor (add one if there isn't one yet) and pass through any needed arguments from the `Original` class constructor.

### e. Compile single file and commit.

## 4. Move methods to the `OneResponsibility` class

For each method you want to move, starting with the leaf methods (i.e. the ones that don't call other `Original`-class methods):

### a. Cut the method declaration from the `Original` class and paste it into the public section of the `OneResponsibility` class.

In Original.h:

```cpp
class OneResponsibility
{
public:
    void MovingMethod();

private:
    int m_movingField;
};

// ... Original no longer contains MovingMethod ...
```

### b. Change the moved method definition from `Original::` to `OneResponsibility::`.

Do not physically move the method definition in the file yet. This will make code-review diffs much easier.

### c. Update references

1. Remove "m_helper." from references to `OneResponsibility` fields in the moved method.
2. In the remaining `Original` class methods, add `m_helper.` to each call to the moved method.

In Original.cpp:

```cpp
void Original::StayingMethod()
{
    m_helper.MovingMethod();
}

void OneResponsibility::MovingMethod()
{
    m_movingField = 42;
}
```

### d. Compile single file and commit.

## 5. Remove `friend` declaration from `OneResponsibility` class

1. Remove `friend` declaration.
2. Compile single file.
3. Adjust visibility of OneResponsibility-class methods as needed.
4. When done, check in to your task branch. So far, little code will have physically moved in your files, making code-review diffs relatively easy to read.

## 6. Move `OneResponsibility` class to its own files

See [Move Class to Its Own Source Files (fix link)](#).
