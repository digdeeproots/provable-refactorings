{{< Conceptul >}}
{{< Unsafe reason="Depends on perfect method name resolution, which we don't often have. Easy to make a subtle mistake." >}}

We might be able to eliminate the risk by executing the move in 4 steps:
1. Move it from the current context to an empty context.
2. Bring in everything that you need in order to resolve all the names. Copy from the source location.
3. For each symbol you had to resolve, try to use it in the target context and make sure you get a compiler error (aka, the name is free to be bound in that context).
4. Move it from the empty context to the target context. Copy in all the name resolution stuff with it.

Thoughts?

# Constraints

* All references to the method are discoverable.
* The method is being moved within the same linked unit of code (i.e. the same C++ module).
  * While it's possible to move methods between modules, it involves a few more steps: you may have to update CMake files and add DLL export directives. This recipe avoids that complexity, for now.
* The method does not refer to any symbols defined in the compilation unit. It only relies on symbols defined in #include files.
  * If it does have local dependencies, you will need to move the other symbols first.

# Recipe

## 1. Remove from anonymous namespace

If the function is at the TOP of an anonymous namespace, cut and paste it above the namespace.

If the function is at the BOTTOM of an anonymous namespace, cut and paste it below the namespace.

If the function is in an anonymous namespace with declarations above and below it:

Insert a closing brace before the function, to close the anonymous namespace, and re-open it below.

For example,

``` cpp
}  // <------ closes anonymous namespace

MyFunction()
{
}

namespace { // <------ re-opens anonymous namespace
```

## 1. Create method declaration

Apply the Split function declaration and definition refactoring. You will leave the declaration behind so that the code in the source location continues to compile.

## 2. Cut method

Cut the method definition out of the source file.

Remember what namespace it was nested within.

## 3. Paste method

a. Locate a namespace that matches the one you cut the method from. (Don't paste into an anonymous namespace, though.)
b. Paste the method into that namespace.

## 4. Compile, and fix compiler errors

You may need to add #include files or copy over definitions.

{{% alert %}}
Mark the change as 'risky'

This recipe is risky because moving a method can cause subtle differences in symbol name resolution: a symbol in one context might have a different meaning than a symbol in another context. The recipe does not currently protect against such meaning changes, nor even look for them.

When you check in, your comment should say:

!!!    move method
{{% /alert %}}
