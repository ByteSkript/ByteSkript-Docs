---
description: Type templates with promised functions that multiple real types can use.
---

# Template Types

{% hint style="danger" %}
This is an **advanced** feature.
{% endhint %}

Template types can be used to define common elements that multiple types will use. They can also be used to create type hierarchies.

All objects have one concrete (real) type. An object may have multiple template types.

### Template Type Structure

Template types have a single name input in their member pattern. The type member goes at the root level of the script (with no indent.)

```clike
template type %TypeName%:
    
```

This name is written in standard, `a-z0-9_` format, without any quotes or brackets.

```clike
template type Thing:
    
```

{% hint style="info" %}
For clarity, example type names are written in `UpperCamelCase` but this is only a style choice to prevent confusion with functions.
{% endhint %}

A template type can hold function members inside it, which need to be indented by an extra unit.

Functions inside the type are considered to **belong** to that type. These are different from regular functions, but the format for writing them is the same.&#x20;

```clike
template type Thing:
    function my_func:
        trigger:
            print "hello"
```

Unlike [regular type functions](types.md), template functions do **not** need to have a trigger.

```clike
template type Thing:
    function my_func:
    function another_func:
```

An empty template function **cannot** be directly run.

These functions may still have return types and parameters.

```clike
template type NamedThing:
    function set_name (name):
    function get_name:
        return: String
```

Template types may use other templates.

```clike
template type Shape:
    function get_sides:

template type Quadrilateral:
    template: Shape
    function get_sides:
        trigger:
            return 4
```

### Using Template Types

A template has two core uses.

#### 1. Categorising and Identifying Objects

Template types can form a hierarchy structure.

```clike
template type Shape:
    function get_sides:

template type Quadrilateral:
    template: Shape
    function get_sides:
        trigger:
            return 4

type Square:
    template: Quadrilateral
```

This can be used to provide information about objects of these types.

```clike
set {var} to a new Square
assert {var} is a Quadrilateral // Square uses Quadrilateral
assert {var} is a Shape // Quadrilateral uses Shape
assert {var} is an Object // all types are Objects

if {var} is a Shape:
    print get_sides() from {var}
```

#### 2. Avoid Repeating Code

When a type uses a template, it inherits all of its functions. This allows multiple different types to use the same code, without needing to copy it.

```clike
template type Quadrilateral:
    template: Shape
    function get_sides:
        trigger:
            return 4

type Square:
    template: Quadrilateral

type Rectangle:
    template: Quadrilateral
```

In this example structure, both `Square` and `Rectangle` will inherit the `get_sides()` function from their shared template `Quadrilateral`.

```clike
set {square} to a new Square
set {rectangle} to a new Rectangle

assert get_sides() from {square} is 4
assert get_sides() from {rectangle} is 4
```

This default function can be overridden by a type that uses the template, simply by declaring an identical function.

```clike
template type Shape:
    function get_sides:
        trigger:
            return 2

type Square:
    template: Shape
    function get_sides: // overrides Shape's get_sides()
        trigger:
            return 4
```

{% hint style="danger" %}
The function must be **exactly** identical to properly override. This includes specifying the same return-type.
{% endhint %}

By using template functions with **no** trigger, more advanced behaviour can be created.

These template functions assure us that a matching function _will_ exist, but provide no behaviour or code for one.

{% hint style="info" %}
Template functions are 'promised' - they don't exist yet, but promise that the type using this template is going to have that function.
{% endhint %}

The actual trigger for this template function must be added to the type using this template.

```clike
template type Shape:
    function get_sides: // promised function
    function describe:
        trigger:
            set {sides} to get_sides() from this object
            print "This shape has " + {sides} + " sides!"

type Triangle:
    function get_sides: // keeps the promise
        trigger:
            return 3
```

With this structure, we can now run the `describe()` function from `Shape` but it will use the `get_sides()` trigger from `Triangle`.

```clike
set {shape} to a new Triangle
run describe() from {shape} // runs Triangle's describe()
```
