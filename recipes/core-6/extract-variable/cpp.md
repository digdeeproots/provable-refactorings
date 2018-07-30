{{< Concepts >}}

# Extract Variable

Extract an expression to be stored into a write-once variable, then read once.

# Constraints

Introducing a variable can change the order of operations. Here are some examples to watch out for:

* Conditional operators (||, &&) short-circuit. Either extract the whole expression, or extract as a lambda.
* Function calls (including overloaded operators) have side effects. Extract as a lambda.
* Increment/decrement operations (i++) for variables that are used elsewhere in the statement. Only extract if you can take (or leave behind) every reference to that variable from the statement.

# Recipe

If there are side effects in the expression you want to extract, extract a lambda instead (See [Extract Function (fix link)](#)).

Otherwise:
1. Select the expression you want to extract.
2. CUT
3. Type `applesauce`
4. On the line above, type `auto applesauce = ` and PASTE and type `;`
  * NOTE: If the extracted expression yields a reference, and the lifetime of the referenced object is managed by something else, then you should type auto& instead of auto.