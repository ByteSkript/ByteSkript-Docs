---
description: Data values directly written in the script source code.
---

# Literals

Any value written into the script source itself is a **literal**.

A literal is an absolute value stored in the code itself, rather than extracted from some expression during the program run-time.

{% hint style="info" %}
Some expressions _act_ as literals by writing a raw value into the compiled source code, but are not included in this section.
{% endhint %}

### Boolean

Booleans are the true/false value-types.&#x20;

The two boolean literals are `true` and `false`.

```clike
(true|false)

set {var} to true
if {var} is true:
if {var} is false:
```

{% hint style="success" %}
Occasionally, booleans can be exchanged with other types. The `true` and `false` values can be equated with `1` and `0` in some syntax.\
For some boolean comparisons, `null` will count as a `false` value.
{% endhint %}

### String

Strings are the most common text value-type.

Strings are written inside `"<text>"` double-quotes.

```clike
set {var} to "my text here"
if {var} is "hello":
if "hello" contains "h":
```

Strings do **not** support parsed inputs. Instead, they may be joined with the `+` operator.

```clike
set {var} to "hello " + "there"
set {var} to "hello " + {name}
set {var} to "hello" + newline + ":)"
```

This is different from [original Skript](https://github.com/SkriptLang/Skript). A simple comparison is given below, for users that need help converting scripts to this format.

| ByteSkript Version                | SkriptLang Version          |
| --------------------------------- | --------------------------- |
| `"hello " + {name}`               | `"hello %{name}%"`          |
| `"hello " + my_func() + " there"` | `"hello %my_func()% there"` |
| `newline + ":)"`                  | `"%newline%:)"`             |

### Integer

Integers are whole (non-decimal) numbers.

Integers are written directly in standard `0-9` numerals.

```clike
set {var} to 5
if {var} is -443:
if 66 > 21:
set {var} to {var} + 23
```

{% hint style="info" %}
Integers are between `-2147483648` and `2147483647`. If you need a smaller or larger value, use a long.
{% endhint %}

### Double

Doubles are decimal numbers.

Doubles are written directly in standard `0-9` numerals. Almost all decimals should be handled as double form.

```clike
set {var} to 5.5
if {var} is 4.0:
if 66.5 > 21.5:
set {var} to {var} + 23.0
```

### Long

Longs are whole (non-decimal) numbers.

Longs are written directly in standard `0-9` numerals with the `L` suffix. Longs have a much greater limit than integers, but take up twice the space in memory.

```clike
set {var} to 5L
if {var} is -443L:
if 66L > 21000000000000L:
set {var} to {var} + 2L
```

### Float

Floats are smaller, less-precise decimal numbers.

Floats are written directly in standard `0-9` numerals with the `F` suffix. Floats take up less space in memory than doubles, but support fewer decimal places.

```clike
set {var} to 5.5F
if {var} is 4.0F:
if 66.5F > 21.5F:
set {var} to {var} + 23.0F
```

### RegEx

Regular expressions are a standard form of text-matching pattern.

RegEx is written inside `/pattern/` slash-characters. A `/` character inside the pattern must be escaped with a backslash `\`.

```clike
set {var} to /hello there/
set {var} to /.+?/
set {var} to /[0-9]/
```

{% hint style="danger" %}
Be careful! A double `//` marks a line-comment!
{% endhint %}

### Null

The `null` value is an empty value, used when a variable or expression has no value.

```clike
(null|none)
set {var} to null
set {var} to none
if {var} is null:
if {var} is none:
```

The `null` value is equal to other `null` values (all null values are the same - nothing.)
