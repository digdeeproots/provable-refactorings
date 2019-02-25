{{< Draft >}}

# Extract Function

This recipe is designed to do Extract Function and nothing else. If you want something else, there are other refactorings for that.

You can commit at the end of any numbered step. At each such point, the code will compile and produce exactly the same results as the original code.

# Constraints

Currently as described the recipe will likely not always work in the given situations.

1. Extracting a block with a return statement.
1. Extracting a block using `this`.
1. Extracting an expression.
1. Extracting a statement.

# Recipe

## 1. Ensure no shadowed variables

To avoid variable name collisions in future steps need to ensure that there are no shadowed variables. Tslint has a rule to do this, called `no-shadowed-variable` ensure the rule is both enabled globally and not locally disabled in the module (file) where the extraction is to be performed.

For each case of this error, perform a `rename` of the offending variable to a unique name.

## 2. Introduce a lambda

For a block/for/while/if, surround with:
 
```ts
(() => {
    // ...
})();
```

Compile. Possible errors:

* `Cannot find name '...'.` Error occurring after the extracted block. This means a variable defined in the block is used after the block and now needs to be returned from the variable. Resolve with 2.a.
* `Jump target cannot cross function boundary.` You have a break/continue. Back up and either [Eliminate Early Return/Continue/Break (fix link)](#) or extract something different. 

### 2.a. Fix `Cannot find name '...'.` compiler errors

For each unique error add the variable to the return statement of the block and assign it to declared variables with the same name.

In this example `Cannot find name 'bar'.` and `Cannot find name 'foo'.` occurred after the block, compiler error occurs somewhere after the block.

```ts
let bar, foo;
({bar, foo} = (() => {
    // ...
    return { bar, foo };
})());
```

This will result in the declared variables in the block having `no-shadowed-variable` tslint errors. Which if it is desired to check in at this point can be resolved by `rename`ing the now shadowed variables to unique names.

## 3. Extract Variable

1. Assign the lambda to `applesauce` and call it.  ([Extract Variable (fix link)](#))

For example,

```ts
(() => { 
    // ...
})();
```

becomes:

```ts
const applesauce = () => {
    // ...
};
applesauce();
```

Compile to make sure you did not make a typo.

## 4. Move the lambda definition to the desired scope

For example, if in a function block, can move the lambda to the module scope.

```ts
function functionToExtractFrom() {
  const applesauce = () => {
      // ...
  };
  applesauce();
}
```

becomes:

```ts
function functionToExtractFrom() {
  applesauce();
}

const applesauce = () => {
  // ...
};
```

Compile. Possible errors:

* In the moved lambda block `Cannot find name '...'.`. This means a variable deceleration is no longer in scope. Resolve with 4.a.

### 4.a. Fix `Cannot find name '...'.` compiler errors

For each missing variable deceleration add the variable to the parameter list and pass it in at the calling location.

For example if `Cannot find name 'bar'.` compiler error is occurring (and `bar` is a `string`).

```ts
function functionToExtractFrom() {
  applesauce(bar);
}

const applesauce = (bar: string) => {
  // ...
};
```

Also check if the variable is assigned to within the moved lambda block. If so it must be returned and reassigned back to its original variable definition.

For example if in addition to `Cannot find name 'bar'.` there is a `Cannot find name 'foo'.` compiler error occurring and `foo` is a `number` that is reassigned in the block.

```ts
function functionToExtractFrom() {
  ({ foo } = applesauce(bar, foo));
}

const applesauce = (bar: string, foo: number) => {
  // ...
  return { foo };
};
```

## 5. (Optional) Convert lambda syntax to traditional function syntax

For example:

```ts
const applesauce = (bar: string, foo: number) => {
  // ...
  return { foo };
};
```

becomes:

```ts
function applesauce(bar: string, foo: number): { foo: number } {
  // ...
  return { foo };
};
```
