---
description: A value-storing configuration file.
---

# Config (csk)

Skript configuration `.csk` files are designed for holding simple program properties that a user may want to edit. While they may be read and written to, they are not designed for storing large amounts of data since the entire file is loaded into memory during the read operation.

Configuration files are not designed to hold executable code, despite following a similar structure and layout to scripts with their verbose `key: value` format.

Files may contain single-line `// comments` or multi-line `/* comments */`.

{% hint style="success" %}
Comments are supported by the **schema**, which means a program can read and write them.
{% endhint %}

By nature, all data values are raw text, but are particularly easy to verify and convert using the `[parsed] as a[n] %Type%`  conversion syntax.

The schema currently supports only single-line `key: value` entries, but support for collections is intended in a future version.

### Entries

Most data is stored in a raw `key: value` format.

The values can contain any characters except new-lines. The value will be read from the first un-escaped colon character `:`.

```
key: value
key two: another value
key \: three: a third value

this line will be ignored, it has no key or value
```

### Lists

{% hint style="danger" %}
This feature is not yet present, but provided as an example users can expect.
{% endhint %}

Lists may have multiple values under a single heading. These will be interpreted as a single text if read directly, but separated when read as a list.

```
my list:
    thing one
    thing two
    thing three
```

### Maps

{% hint style="danger" %}
This feature is not yet present, but provided as an example users can expect.
{% endhint %}

Maps may have multiple key-value entries under a single heading. These will be interpreted as a single text if read directly, but separated when read as a map.

```
my map:
    key one: value
    key two: value
```
