---
description: >-
  User-defined object types, which can hold special object-local functions and
  other properties. Useful for interacting with libraries from other JVM
  languages, like Java and Kotlin.
---

# Types

{% hint style="danger" %}
This is an **advanced** feature.
{% endhint %}

Types are used to create objects. ByteSkript has a large number built in (like `String`, `Number`, `Boolean`, etc.) but the type member allows a user to define their own custom type.

All objects have at least one type. The object `"hello"` is a `String`. This information is used for syntax like `"hello" is a string`. Most types allow a new object to be created, such as lists and maps.

### Type Structure

Types have a single name input in their member pattern. The type member goes at the root level of the script (with no indent.)

```clike
type %TypeName%:
    
```

This name is written in standard, `a-z0-9_` format, without any quotes or brackets.

```clike
type Thing:
    
```

{% hint style="info" %}
For clarity, example type names are written in `UpperCamelCase` but this is only a style choice to prevent confusion with functions.
{% endhint %}

A type can hold function members inside it, which need to be indented by an extra unit.

Functions inside the type are considered to **belong** to that type. These are different from regular functions, but the format for writing them is the same.&#x20;

```clike
type Thing:
    function my_func:
        trigger:
            print "hello"
```

A type can have multiple functions inside it. These functions may have parameters.

```clike
type Thing:
    function my_func:
        trigger:
            print "hello"
    function second_func (word):
        trigger:
            print "hello " + {word}
```

A type can be created and used in another function. This uses the `[a] new %Type%`

```clike
type Thing:
    function my_func:
        trigger:
            print "hello"

function say_hello:
    trigger:
        set {thing} to a new Thing // makes a new object
        assert {thing} exists
        assert {thing} is a Thing // it's of our type
```

Calling functions that belong to a type works slightly differently.

This uses the `function(...) from %Object%` syntax.

```clike
type Thing:
    function my_func: // this will be run
        trigger:
            print "hello"

function say_hello:
    trigger:
        set {thing} to a new Thing
        assert {thing} exists
        run my_func() from {thing} // runs my_func
        // hello was printed
```

Currently, printing the object directly with `print {thing}` will not look very nice. Special string conversion behaviour can be added using a special `toString` function.

```clike
type Thing:
    function toString: // this is a special function
        return: String // this is necessary here
        trigger:
            return "My Cool Thing"

function say_hello:
    trigger:
        set {thing} to a new Thing
        assert {thing} exists
        print {thing} // this will run our toString function internally
```

{% hint style="info" %}
The `toString` function is overriding the built-in `Object` type's `toString` method, and allowing the user to control what it prints.

The `return: String` is necessary to make sure the compiler knows exactly which function to override.
{% endhint %}

As type functions are local to that particular type, two separate custom types may define identical functions without issue.

```clike
type Thing:
    function hello:
        trigger:
            print "hello"

type Box:
    function hello:
        trigger:
            print "goodbye"
```

### Templates

Types may use multiple [template types](template-types.md).

```clike
type Square:
    template: Shape
    template: Thing
    function hello:
        trigger:
            print "hello"
```

An explanation of how these work can be found [here](template-types.md).

### Properties

Types may declare properties. These are value-storing members of the type, that function a bit like variables attached to each object.

{% hint style="success" %}
Unlike variables, an individual property exists for each object of the custom type.
{% endhint %}

```clike
type Shape:
    property name:
    property size:
```

These properties can be used and set with the `<property> of %Object%` syntax.

```clike
set {thing} to a new Shape
set name of {thing} to "Square"
set size of {thing} to 100
assert name of {thing} is "Square"
assert size of {thing} is 100
```

You may specify the type of these properties, so that they will only accept a certain type of object.

```clike
type Shape:
    property name:
        type: String
    property size:
        type: Number
```

If the property is set to the wrong type, an error will appear. This can be used to prevent other scripts from wrongly-using the type.

```clike
set {thing} to a new Shape
set name of {thing} to "Square" // fine
set name of {thing} to 42 // error
```

The properties of two different objects of the same type are separate. They can have different values.

```clike
set {a} to a new Shape
set {b} to a new Shape
assert {b} is not {a}
set name of {a} to "Square"
set name of {b} to "Circle"
assert name of {a} is "Square"
assert name of {b} is "Circle"
```

Two different types can have a property with the same name.

```clike
type Person:
    property name:
        type: String

type Car:
    property name:
        type: String
```

A type can have both functions and properties.

```clike
type Person:
    property name:
        type: String
    function say_hello:
        trigger:
            print "hello " + this object + "!"
```

