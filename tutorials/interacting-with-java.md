---
description: >-
  Using ByteSkript to interact with Java and other JVM programming languages
  directly.
---

# Interacting with Java

Since ByteSkript runs directly in the Java Virtual Machine (rather than interpreting scripts) all resources from the Java Development Kit and other third-party libraries are available when scripts are running.

One of the major design goals for ByteSkript's language implementation was to allow advanced users to interact with Java libraries directly, rather than needing pre-created syntax for this.

{% hint style="danger" %}
This is an **advanced** feature.
{% endhint %}

### Calling Java Methods

There are two ways to call a Java method: either through the [external function syntax](../language-syntax/effects/flow-control-effects.md#run) or through a handle from the [Skript function library](../namespaces/skript.md#handles).

For this tutorial we will be using the external function syntax.

```clike
run function_name(args...) from org/example/Class
run methodName() from org/example/Class
set {var} to nanoTime() from java/lang/System
```

Java has two (main) types of method, which we will need to access slightly differently.

#### Calling Static Methods

To call a static method (which will look like `public static Thing myMethod(Params..)` in the Java library) we just need its class path.

To call `System.nanoTime()` we would need the `System` class's path (`java.lang.System`) and the method name (`nanoTime`).

We can then call it as we would an external Skript function from a different script file.

```clike
set {var} to nanoTime() from java/lang/System // calls our method
```

{% hint style="info" %}
This is identical to using a Skript function because scripts are compiled to classes before being run.
{% endhint %}

If we needed to pass arguments to the Java method, they can be entered as normal.

Arguments can be written directly or passed in variables, expressions, etc.

```clike
run myMethod("hello", 6) from my/java/Class
set {word} to "hello"
set {number} to 6
run myMethod({word}, {number}) from my/java/Class
```

Unlike other JVM languages, ByteSkript does not need to know the parameter/argument types to run the method.

You can also pass arguments in atomic variables without an issue - these will be automatically unpacked where necessary.

```clike
set {@word} to "hello"
set {@number} to 6
run myMethod({@word}, {@number}) from my/java/Class
```

#### Calling Dynamic Methods

The majority of Java libraries will use objects, and to interact with them you will be required to call **dynamic** (non-static) methods.

{% hint style="info" %}
These belong to a particular object, rather than the class itself. Their behaviour can change between objects.
{% endhint %}

These are called with similar syntax, but we use the target object rather than its class.

This is the same syntax that is used for calling member functions from a [custom type](../language-syntax/members/types.md#type-structure).

```clike
run setName("Bob") from {person}
run println("hello!") from {output}
run run() from {runnable}
set {var} to get() from {supplier}
```

### Creating and Using Classes

{% hint style="warning" %}
This section is designed for **very advanced** users.
{% endhint %}

Many libraries written in Java and other JVM languages will require extending, implementing or creating classes.

ByteSkript [types](../language-syntax/members/types.md) are equivalent (and compile) to Java **classes**. [Template types](../language-syntax/members/template-types.md) are equivalent (and compile) to Java **interfaces**.

### Using Existing Java Interfaces

If a library requires you to implement an interface, you can use this interface as a template for your custom type.

In the example below, the `java.util.function.Supplier<?>` interface is being implemented, and the `T get()` method is being overridden.

```clike
type Getter:
    template: java/util/function/Supplier // we need the full path to it here
    function get:
        trigger:
            return "hello"
```

We can now make one with `a new Getter` and use its functions.

```clike
type Getter:
    template: java/util/function/Supplier // we need the full path to it here
    function get:
        trigger:
            return "hello"

function test:
    trigger:
        set {getter} to a new Getter
        assert {getter} is a Getter // it's our custom type
        assert {getter} is a java/util/function/Supplier
        // it's also that Java type
        print get() from {getter} // runs the get() function
```
