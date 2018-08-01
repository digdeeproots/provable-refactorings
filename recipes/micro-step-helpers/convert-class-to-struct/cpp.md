{{< Concepts >}}

# Convert class to struct

# Constraints

* All members are simple public members.

# Recipe

## 1. Update all forward declarations

Find all usages of the type.  Replace the word 'class' with 'struct' wherever it occurs.

## 1. Update primary declaration

## 2. Remove the public keyword

It is now the default and all members are public anyway.

## 3. Compile.
