---
description: >-
  A short guide for converting code from SkriptLang's original Skript to the
  language format used by ByteSkript. This also contains a list of the major
  differences between the two.
---

# Converting to ByteSkript

While an effort has been made to maintain some (superficial) parity, ByteSkript's language implementation is **not** compatible with SkriptLang's version (original Skript.)

Some of these differences are changes to the appearance or format of the Skript language. Others are functional changes to the behaviour of certain elements.

The main differences (and how to convert to ByteSkript's format) are listed on this page.

### Triggers

#### Description

All code-containing elements require a `trigger:` section in ByteSkript. This includes events, functions, etc.

All of the runnable code goes inside this `trigger:` entry.

#### Comparison

{% tabs %}
{% tab title="ByteSkript Format" %}
```clike
on script load:
    trigger:
        print "hello"

function myFunc():
    trigger:
        print "hello"
```
{% endtab %}

{% tab title="SkriptLang Format" %}
```vbnet
on script load:
    broadcast "hello"

function myFunc():
    broadcast "hello"
```
{% endtab %}
{% endtabs %}

#### Change Reason

ByteSkript supports other entries in these members, as well as the runnable trigger.

For example, the function supports a `verify:` entry for start-up testing.

### Minecraft Syntax

#### Description

Unlike original Skript, ByteSkript is not a [Minecraft](https://minecraft.net)/Bukkit domain-specific language.&#x20;

ByteSkript provides no Minecraft-related syntax. ByteSkript is not a Bukkit server plug-in.

{% hint style="info" %}
Third-party language libraries or modified versions may offer Minecraft support.
{% endhint %}

#### Change Reason

ByteSkript's design philosophy is to be a simple, serviceable scripting language for the Java Virtual Machine.

### Variables

#### Description

ByteSkript's regular `{var}` variables are **local** to the current trigger. This is comparable to original Skript's `{_local}` variables.

ByteSkript's variables are strictly-named and do not support inputs in their names.

ByteSkript has [multiple variable types](converting-to-byteskript.md#blob.) with special use-cases.

#### Comparison

{% tabs %}
{% tab title="ByteSkript Format" %}
```clike
set {!var} to true // global, in all places
set {var} to true // local to this trigger
set {@var} to true // atomic, can be passed to other triggers
set {_var} to true // thread-local, anywhere on this process
```
{% endtab %}

{% tab title="SkriptLang Format" %}
```vbnet
set {var} to true # global, accessible anywhere
set {_var} to true # local, accessible in this trigger
```
{% endtab %}
{% endtabs %}

#### Change Reason

Original Skript's variables were not easily-applicable to the JVM environment, and would have made ByteSkript significantly slower than other JVM languages.

ByteSkript still supports a similar form with `{!var}` global variables.

See the [variables](converting-to-byteskript.md#blob.) page for examples.

### Lists

#### Description

ByteSkript does not support original Skript's `{list::*}` variables. Instead, regular variables can be set to list objects.

#### Comparison

{% tabs %}
{% tab title="ByteSkript Format" %}
```clike
set {list} to a new list
add "hello" to {list}
add "goodbye" to {list}
set index 1 in {list} to "there"
clear {list}
```
{% endtab %}

{% tab title="SkriptLang Format" %}
```vbnet
add "hello" to {list::*}
add "goodbye" to {list::*}
set {list::2} to "there"
clear {list::*}
```
{% endtab %}
{% endtabs %}

#### Change Reason

Original Skript's list variables were not easily-applicable to the JVM environment, and would have made ByteSkript significantly slower than other JVM languages.

Some beginners found the nested list/map structure of original Skript unnecessarily confusing, and ByteSkript's more explicit version should make this clearer.

### Loops

#### Description

ByteSkript has a slightly different loop syntax that is designed to be clearer and simpler to use.

#### Comparison

{% tabs %}
{% tab title="ByteSkript Format" %}
```clike
set {list} to a new list
add "hello" to {list}
add "goodbye" to {list}
loop {word} in {list}:
    print {word}

loop 5 times:
    print "hello"
```
{% endtab %}

{% tab title="SkriptLang Format" %}
```vbnet
add "hello" to {list::*}
add "goodbye" to {list::*}
loop {list::*}:
    broadcast loop-value

loop 5 times:
    broadcast "hello"
```
{% endtab %}
{% endtabs %}

#### Change Reason

This was changed along with lists to be clearer and easier to understand for beginners.

### Function Declarations/Calls

#### Description

ByteSkript has some minor changes to function declarations and calls.

#### Comparison

{% tabs %}
{% tab title="ByteSkript Format" %}
```clike
function no_args_func: // no () required here
    trigger:
        print "hello"
        run normal_func("hi") // 'run' effect required

function normal_func (thing): // no parameter types specified
    trigger:
        print {thing}
```
{% endtab %}

{% tab title="SkriptLang Format" %}
```vbnet
function no_args_func():
    broadcast "hello"
    normal_func("hi")

function normal_func(thing: string):
    trigger:
        broadcast {thing}
```
{% endtab %}
{% endtabs %}

#### Change Reason

Part of making ByteSkript's language stricter and more regular meant making sure every line was explicitly an effect, and not an expression. This meant the `myFunction()` dual effect/expression from original Skript had to be altered.

The `run %Executable%` [effect](../../language-syntax/effects/flow-control-effects.md#run) is also used for running other things such as lambdas, dynamic functions and background tasks.
