{{< Draft >}}

# Resolve Feature Envy on New Whole Value

You will use this after introducing a [Whole Value](http://c2.com/ppr/checks.html#1) to repair an instance of [Primitive Obsession](http://wiki.c2.com/?PrimitiveObsession). It can also show up any time you are treating an object like a primitive and you want to treat it a bit more like a whole value.

# How

There are several different cases you might encounter. In each case the method has multiple lines that use fields on our Whole Value. We will extract the code that uses the Whole Value and move it to that class.

Look for one of the following cases:

|Case|How to spot|Remediation|
|--- |--- |--- |
|**Wrong Place**|Most or all of the function uses values on the Whole Value.|`Move Method`|
|**Uses Whole Value At End**|Method has only one chunk that interacts heavily with the Whole Value, and it is located at the end.|`Extract Method` then `Move Method`|
|**Multi-purpose Method**|Method has one chunk that interacts heavily with the Whole Value, and other independent chunks that interact with other objects.|Multiple extractions (see below)|
|**Chaining Data**|Method interacts with the Whole Value, gets an intermediate result, then uses that with another object|Split up the sequence (see below)|

### Multi-purpose Method

The interactions with the Whole Value may not be all together. That sequencing could be necessary or unimportant. Follow the [Extract and Move recipe](http://devmockup/cbr/recipes/extract_and_move.html) to take care of the details.

### Chaining Data

Use this when the method interacts with the Whole Value, gets an intermediate result (probably represented in several local variables), and then uses that intermediate result with other objects later in the function.

There are 3 options here:

* Use multiple return values.
* Introduce another Whole Value to represent the intermediate result.
* Use a callback.

### Breaking Apart a Chain Using Multiple Return Values

(Does not work in Java; works in any language with multiple returns or out / ref params)

Use the [Extract and Move recipe](http://devmockup/cbr/recipes/extract_and_move.html) for interactions with the Whole Value. You will end up with several local variables holding the intermediate results.

### Breaking Apart a Chain Using Another Whole Value

1.  `Extract Method` on the code that uses the intermediate values.
2.  `Introduce Whole Value` on the method you just extracted. For purpose of this recipe, we’ll refer to this type as `struct Intermediate`.
3.  `Introduce Variable` on the `Intermediate` variable.
4.  `Extract Method` on all the statements that use the original Whole Value, including the variable declaration for the `Intermediate`.
5.  Optionally `Inline` the method you extracted in step 1.

### Breaking Apart a Chain Using a Callback

1.  `Extract Method` on the code that uses the intermediate values. This creates the method that will eventually become a callback.
2.  `Extract Method` on the entire method body.
3.  `Make Method Static` on the method you just extracted (which we will call `Inner` for this recipe). Now you are explicit about the data you are using from the original object.
  * Make sure to pass and use fields, rather than passing only the entire object. You will still need to pass the entire object in order to call the callback, but that callback should be the only time the method uses the object.
4.  `Move Method` on `Inner` to the Whole Value. The result is that the new method is on the Whole Value and makes a call back to the original object.
5.  Clean up parameters and `Inline` any intermediate methods if desired.
