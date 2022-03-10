---
description: >-
  A brief introduction to writing advanced syntax using Java Virtual Machine
  bytecode, for maximum efficiency and greater control.
---

# Advanced Syntax (Bytecode)

### Advantages

1. Syntax written using the bytecode assember directly can be more efficient than using a handler method.
2. For small or commonly-repeated tasks, a significant amount of overhead can be reduced by doing the operation directly.
3. Library developers have much greater control over how tasks are run.

### Write Operation Structure

If more complex behaviour is required than a method invocation, syntax can interact directly with the **assembler**, which uses [Foundation](https://github.com/Moderocky/Foundation) for accessibility.

There are two entry-points for accessing this, during the **first** and **second** pass.

{% hint style="info" %}
ByteSkript's compiler runs in two orders.

The **first** pass works outer -> inner, left-to-right.

The line `print 1 + 2` would be run in:

1. `print %Object%`
2. `%Object% + %Object%`
3. 1 (literal)
4. 2 (literal)

The **second** pass works inner -> outer, left-to-right.

The line `print 1 + 1` would be run in:

1. 1 (literal)
2. 2 (literal)
3. `%Object% + %Object%`
4. `print %Object%`
{% endhint %}

Almost all instruction assembly will be done on the **second** pass: this follows the natural order of bytecode instructions. The **first** pass is typically used for providing lookahead information to inputs, when special behaviour is required.

E.g. the `set ...` effect uses the **first** pass to tell the first input expression to use the `SET` handler.

The **first** pass uses the `preCompile` method, and the **second** pass uses the `compile` method.

If we wanted to implement special behaviour, we can override this.

```java
@Override
public void compile(Context context, Pattern.Match match) {
    context // the compile context of this use
        .getMethod() // the method assembler
        .writeCode(pushNull()); // the aconst_null bytecode instruction
    context.setState(CompileState.STATEMENT); // tell the compiler we're still in a line of code
}
```

In this example we are pushing a `null` value onto the stack ('returning' it from our expression.)

The `Context` provides a lot of information about what's going on at this exact point in the script, giving us access to variables available, the programmatic flow tree (if/elses, loops, etc.) and a lot more.

This is also how we access the method assembler where the code is being written, using `getMethod`.

The `writeCode` instruction puts a [Foundation](https://github.com/Moderocky/Foundation) `WriteInstruction` into the assembler. During the final compilation (done after the entire script has been parsed), this will write the bytecode.

The `pushNull` write instruction pushes an `aconst_null` (null) value onto the stack, making it available for whatever is using this expression.

At the end of our expression we need to tell the compiler we're in a `CompileState.STATEMENT` state, so it knows what to look for next. While this is default, it is important in case an inner expression has changed this for some reason.

For very advanced users, bytecode can be written directly with [ASM](https://asm.ow2.io) in this compile method.

```java
@Override
public void compile(Context context, Pattern.Match match) {
    context
        .getMethod()
        .writeCode((writer, visitor) -> {
            visitor.visitIntInsn(16, 4);
            visitor.visitVarInsn(54, 2);
            visitor.visitIincInsn(2, 1);
            visitor.visitVarInsn(21, 2);
        });
    context.setState(CompileState.STATEMENT);
}
```

{% hint style="danger" %}
This is not recommended unless you are experienced with the bytecode layout and the [Virtual Machine instruction set](https://docs.oracle.com/javase/specs/jvms/se17/html/jvms-6.html).
{% endhint %}

This is provided for developers who wish to extend or modify the Skript language at a fundamental level, such as by adding entirely new constructs or features.
