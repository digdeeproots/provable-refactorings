{{< Concepts >}}

# Rename

Rename anything which can be named (function, variable, parameter, field, class, namespace, file, typeclass, enum, ...).

# Constraints

Rename relies on your ability to find all references to a name, and to change them.

For example, a public API published to Tableau customers has names that you will be unable to change. Or convention-over-configuration, eval, dependency injection, and reflection can make it impossible to trace references to a name by transforming the binding between name and item to be dynamic instead of static.

# Recipe

1. If renaming a type or namespace where it matches the name of the file, first rename the file. And check in, to make code reviews easier.
2. Find all references to all symbols named either your original or new name. Save them somewhere.
3. Find all the references to your symbol.
4. Change the name at all locations.
5. Re-find all references to all symbols named original or new name. Ensure that each referenc epoint references the same definition point (the labels may ahve shifted, but the code blocks referenced are identical).

It's possible that the rename introduced a change in a subtle way, making step 3 difficult. This is particularly the case when your new name causes subtle collisions, changing the binding of other things.

For example:

``` cpp
void B();
  
namespace
{
    void C();
    void A()
    {
        B();  // the call
    }
}
```

Rename B -> C will cause the call to resolve differently. So you have to confirm that all the references to the original name are still references to the same thing.
 
Rename C -> B will cause the call to resolve differently, even though the rename isn't changing the call. So you also need to confirm that all references to the target name are the same.
