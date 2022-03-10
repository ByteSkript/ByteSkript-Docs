---
description: >-
  This tutorial explains the process of creating small custom syntax that link
  to functions, and making these available to the compiler.
---

# Creating Custom Syntax

This tutorial is designed for people looking to write custom syntax within Skript itself, not as a full external library.

If you are looking to make a full library in Java with advanced syntax, see [here](creating-a-library/) for details.

### Getting Started

ByteSkript allows very basic custom syntax (effects, expressions, properties) to be created.

This is done by adding a special `syntax` entry to a function, so that when the syntax is used it will reference your function internally.

We can create a basic effect by specifying the `effect` pattern.

```clike
function my_function:
    syntax:
        effect: my [cool] effect
    trigger:
        print "hello!"
```

Currently, this effect cannot be used, since the ByteSkript compiler does not know about it. In order to use it we must compile this class and add it to the `libraries/` folder.

This can be done using the [compile](../#compile) command.

`java -jar path/to/ByteSkript.jar compile`

We can then find the script's output in the `compiled/` folder. It will be named `skript.path.to.script.class`.

Once we move this file to the `libraries/` folder, our new syntax will be available to use as `my [cool] effect`.

Every time this effect is used, the compiler will insert a `run` effect to your function.
