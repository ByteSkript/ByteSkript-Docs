---
description: >-
  A very brief overview of ByteSkript's language features, with links to their
  respective documentation pages.
---

# 🗺 Language Tour

Welcome to the short tour of the language. For clarity, this has been divided into a list of sections you can find on the right margin of the page.

{% code title="script.bsk" %}
```clike
on script load:
    trigger:
        print "Welcome to ByteSkript!"
```
{% endcode %}

If you want to follow along with the tour, you can copy any of the examples into a script file and run them locally. Information about installing and running scripts can be found [here](./#installing-byteskript).

{% hint style="info" %}
For those interested in technical information, each section has a **how it works** box where you can learn about how that language feature was created.
{% endhint %}

### Scripts

Every program is made up of one of more scripts. These go inside `scriptname.bsk` files inside the `skript/` directory.

{% hint style="success" %}
Script names must be written in alphaneumeric characters (`a-z0-9`) and allow underscores `_`. They **must** start with a letter (`a-z`).

Valid names: `myscript.bsk`, `cool_thing.bsk`, `MyScript.bsk`, `My_Cool_Script123.bsk`.
{% endhint %}

Your program can have as many scripts in as you like.

<details>

<summary>How it Works</summary>

The script files are compiled to JVM classes, containing methods for each function, event-handler and other members.

</details>

## Syntax Features

An introduction to the core syntax and some of its basic uses.

### Events

Events are triggered externally when something happens. They are used as 'entry points' to a program (to start it running.)

Most scripts will use the `on [script] load` event to start off. Multiple handlers can be added for the same event, which will start **separate** processes.

{% code title="script.bsk" %}
```clike
on script load:
    trigger:
        print "Welcome to ByteSkript!"

on script load:
    trigger:
        print "This is a second entry-point!"
```
{% endcode %}

Click [here](../../language-syntax/members/events.md) to read more about events.

<details>

<summary>How it Works</summary>

Each event trigger is compiled to a method, which Skript will run when the event occurs.

</details>

### Variables

Variables are used to hold data values in your code. They are named inside `{curly}` brackets.

{% hint style="success" %}
Variable names must be written in alphaneumeric characters (`a-z0-9`) and allow underscores `_`. They **must** start with a letter (`a-z`).

Valid names: `{myvar}`, `{my_var}`, `{number}`, `{CoolThing}`, `{var_123}`.
{% endhint %}

Regular variables are **local** to the current trigger. They won't be set in a different trigger.

{% code title="script.bsk" %}
```clike
on script load:
    trigger:
        set {var} to "Hello"
        set {number} to 3
        set {number} to {number} + 2
        print {var} + ", the number is " + {number}
        // Hello, the number is 5
```
{% endcode %}

Variables do not have fixed types: you can store any value in the same variable.

If a variable is used before it is set, it will be empty and have a 'null' value.

{% code title="script.bsk" %}
```clike
on script load:
    trigger:
        print {var} // null
        set {var} to "Hello"
        set {var} to 3
        print {var} // 3
```
{% endcode %}

Click [here](../../language-syntax/expressions/variables.md#value-variables) to read more about variables.

<details>

<summary>How it Works</summary>

Variables take a slot in the local pool, as they do in Java.

Each variable name links to a particular slot number.

</details>

### Functions

Functions are a great way to organise your scripts, rather than putting all the code into one big trigger.

You can give values to a function as **arguments**.

You can have multiple functions with the same name, as long as they have different numbers of parameters.

{% code title="script.bsk" %}
```clike
on script load:
    trigger:
        run my_func()
        run my_func("Bob", 66)
        
function my_func: // no parameters
    trigger:
        print "This is my function!"

function my_func(name, age): // 'name' -> {name}, 'age' -> {age}
    trigger:
        print "My name is " + {name}
        print "My age is " + {age}
```
{% endcode %}

Functions may also give back values to the trigger that called them using the `return` effect.

You can specify the return type to help users to know what to expect, but this is optional.

{% code title="script.bsk" %}
```clike
on script load:
    trigger:
        set {var} to add(1, 2)
        print {var} // 3
        set {var} to add_six({var})
        print {var} // 9
        
function add(x, y):
    trigger:
        return {x} + {y}
        
function add_six(number):
    return: Number // optional!
    trigger:
        return {number} + 6
```
{% endcode %}

Click [here](../../language-syntax/members/functions.md) to learn more about functions.

{% hint style="info" %}
The values you give to a function are called **arguments**.

`run func({arg1}, {arg2})`

The values the function takes are called **parameters**.

`function func(param1, param2)`
{% endhint %}

<details>

<summary>How it Works</summary>

Each function is compiled to a visible static method. These can be called from other functions.

</details>

### Effects

Every line of runnable code inside a script is an **effect**. Most of these lines will take inputs, which come from **expressions**.

Effects do not give back any value, and cannot be used as inputs for other syntax.

{% hint style="info" %}
Effects will usually be verbs or action words like "print", "make" or "run".

You can think of them as instructions for the program.
{% endhint %}

The simplest 'control' effects are the `set...`, `add...`, `delete...`, and `run...` effects.

{% code title="script.bsk" %}
```clike
on script load:
    trigger:
        print "Welcome to ByteSkript!"
        set {var} to 1
        delete {var}
        set {list} to a new list
        add "hello" to {list}
```
{% endcode %}

Click [here](../../language-syntax/effects/) to read more about effects.

<details>

<summary>How it Works</summary>

Most effects compile to a `void` method call which does something with the inputs.

Control effects (like `set`) might delegate the actual behaviour to the input expression. For example, `set {var} to 1` delegates the behaviour to the variable expression's `SET` handler.

</details>

### Expressions

Expressions are any syntax that gives back a value. Some expressions may also accept inputs.

Expressions cannot go on their own line, they need to go inside an effect or another expression.

{% code title="script.bsk" %}
```clike
on script load:
    trigger:
        print the java version // [the] java version is an expression
        print {var} // {var} is an expression
        print "Welcome to ByteSkript!" // "..." is a Literal expression
        print my_func() // the my_func() call is an expression.
```
{% endcode %}

Click [here](../../language-syntax/expressions/) to read more about expressions.

<details>

<summary>How it Works</summary>

Most expressions compile to a method call that returns a value.

Some expressions (like variables) might compile to other special behaviour.

</details>

### Conditions

Conditions are sections that run if (and only if) a certain check passes.

They are split into an `if, else/if, else` tree structure.

{% code title="script.bsk" %}
```clike
on script load:
    trigger:
        set {var} to true
        if {var} is true:
            print "That worked!"
        else if {var} is true:
            print "That worked!"
        else:
            print "Something went wrong..."
```
{% endcode %}

You can also access and re-assign variables in these sections.

{% code title="script.bsk" %}
```clike
function my_func(number):
    trigger:
        if {number} is greater than 6:
            set {var} to "yes"
        else if {number} is 3:
            set {var} to "no"
        print {var} // "yes", "no" or 'null'
```
{% endcode %}

Click [here](../../language-syntax/sections/conditional-if-else.md) to read more about conditions.

<details>

<summary>How it Works</summary>

These sections compile to a set of programmatic 'jumps' that matches the behaviour of Java.

</details>

### Loops

Loops can be used to run code multiple times, according to certain behaviour.

A `while` loop will run until the condition fails.

{% code title="script.bsk" %}
```clike
on script load:
    trigger:
        set {var} to 0
        while {var} is less than 10:
            set {var} to {var} + 2
            print "Counting " + {var}
        print "I counted to " + {var}
```
{% endcode %}

A `loop %Number% times` will loop the specified number of times. (Crazy, right?)

{% code title="script.bsk" %}
```clike
on script load:
    trigger:
        set {var} to 6
        loop {var} times:
            print "hello"
        print "Done!"
```
{% endcode %}

A `loop %Variable% in %Objects%` will loop through the collection of objects, storing each one in the variable and running the loop for it.

{% code title="script.bsk" %}
```clike
on script load:
    trigger:
        set {things} to ("hello", 6, "bean", 4.3)
        loop {thing} in {things}:
            print "The thing is " + {thing}
            if {thing} is a Number:
                print "That's a number!"
        print "Done!"
```
{% endcode %}

Click [here](../../language-syntax/sections/loop.md) to read more about loops.

<details>

<summary>How it Works</summary>

These sections compile in the same way as a Java `for` or `while` loop would.

</details>

### Lambdas

Lambdas can be used to define runnable code inside a trigger.

These can be stored inside variables and run multiple times. They can also be returned from or given to functions.

```clike
on script load:
    trigger:
        set {var} to a new runnable:
            print "hello!"
        run {var} // prints hello
        set {bye} to get_runner()
        run {bye} // prints goodbye
        
 function get_runner:
     trigger:
         return a new runnable:
             print "goodbye!"
         
```

Click [here](../../language-syntax/sections/lambda.md) to read more about lambdas.

<details>

<summary>How it Works</summary>

The lambda code is compiled to an extracted, synthetic method.

When a lambda is created for the first time, a handle is compiled by the lambda metafactory. This is the same process that Java 8+ uses.

This is to avoid adding unnecessary hidden classes to the compiler output.

</details>

### Types

Scripts can define types that are used to create objects.

{% hint style="warning" %}
This is an **advanced** feature.
{% endhint %}

These are most useful for interacting with Java libraries (like Java's JDK) but they can also help advanced users to follow (semi) object-oriented design and prevent repeating code.&#x20;

```clike
type Square:
    function get_sides:
        trigger:
            return 4

on script load:
    trigger:
        set {square} to a new Square
        print get_sides() from {square}
         
```

Click [here](../../language-syntax/members/types.md) to read more about types.

## Language Features

Features of the language itself, rather than specific syntaxes.

### Simple Processes

ByteSkript aims to make it simple to manage timings and threads, without needing to deal with errors or clunky procedures for starting background tasks and getting results.

```clike
print "hello"
run a new runnable in the background:
    print "goodbye"
run my_func() in the background
```

You can communicate easily between processes, allowing you to run actions in the background and notify the main task when it's time to continue.

```clike
print "Going to sleep"
set {@var} to 0
set {thread} to the current process
run a new runnable in the background:
    print "I'll wake you in 10 seconds"
    wait 10 seconds
    set {@var} to 100
    wake {thread} // wakes the main process up
sleep // waits until something else wakes us up
print "Somebody woke me up!"
print "The number is " + {@var} // 100
```

Since ByteSkript runs between multiple processes, variable types that are accessible between processes have to be **atomic** (respect changes from other threads.)

This prevents concurrent modification (where two separate processes try to access the same thing at the same time) and makes race conditions less serious.

```clike
set {@omic} to "hello"
set {runner} to a new runnable:
    wait 1 second
    print {@omic}
run {runner} in the background
set {@omic} to "goodbye"
// goodbye!
```

<details>

<summary>How it Works</summary>

Scripts are run on special threads that allow much easier control and transfer of information.

These threads also have an accessible lock that allows tasks running on other threads to get access and pause/unpause them at will.

For actions that require Java's main method thread, a two-way airlock queue is used that can throttle both threads and effectively 'bind' them together, allowing the script to transfer between running instructions on one or the other.

This design was built with single-threaded programs like [Minecraft](https://minecraft.net) in mind, where using the main thread would be required.&#x20;

A diagram of the airlock procedure is shown below.

![](../../.gitbook/assets/threads.png)

&#x20;Note: not all ByteSkript implementations will use this.

</details>

### Easy Modification

ByteSkript is designed to be modified.

* Third-party libraries written in Java can add new syntax or entire language features.
* Scripts can compile new syntax that links to functions.
* ByteSkript can be forked and applied as a DSL for an existing program.

This makes it possible to apply ByteSkript to a lot of different situations, and customise it to be more suitable for particular tasks. Very few other languages offer language-level modification from within the language itself.

As the Skript language is designed to be English-readable and accessible for beginners, this gives it an advantage over more complicated (and difficult) programming languages.

### Fluid Typing

ByteSkript achieves a mixture of static and dynamic typing using its double-compiler structure.

Scripts are usually written with **dynamic** typing - you don't need to specify types for variables, function parameters, etc.

```clike
set {var} to "hello" // var is a string
set {var} to 3 // var is now a number
```

But they are still able to interact with the JVM, which uses **static** typing.

```clike
set {var} to 3
set {var} to "hello"
run broadcast({var}) from org/example/MyClass // a Java method!
```

<details>

<summary>How it Works</summary>

ByteSkript has **two** compilers: the ahead-of-time (AOT) turns Skript code into `.class` bytecode that the JVM can load, and the other patches up these instructions on-the-fly (OTF) to improve them when the program runs.

ByteSkript's **AOT** compiles the function (or method) call to a dynamic instruction.

When the compiled script is run, the **OTF** compiler fills in the code required to bridge the function call to the Java method, such as by checking types, unboxing atomics and primitives and converting numbers to their correct type (e.g. compressing a Double to an `int`.)

To an extent, this allows ByteSkript to correct and patch up minor errors in code without causing a fatal error or failing to compile like a strongly-typed language would.

</details>

This also applies to functions that belong to an object (like non-static methods in Java.)

This allows you to run matching functions from entirely different objects.

```clike
if {something} is true:
    set {var} to a new list
else:
    set {var} to a new map
set {size} to size() from {var} // size() is a Java method!
print {size} // 0
```

In Java, this structure would be impossible since the `size` method from `Map` and `List` are different - they don't share an interface.

<details>

<summary>How it Works</summary>

ByteSkript uses a design similar to Go for this, where it is only important that a thing _has_ a function, rather than where that function was actually declared.

Using the target compiler from [Mirror](https://github.com/Moderocky/Mirror), a caller is written for each type the first time it is used, and is then re-used for each subsequent call to the same function.

For the first few calls a dynamic callsite will be more efficient, but the virtual instruction catches up in speed as it benefits from JIT.

Eventually, this will be switched to using the bootstrapper once [JEP 8158765](http://openjdk.java.net/jeps/8158765) is released.

</details>
