---
description: >-
  A combination of dangerous low-level functions and trial features for future
  syntax.
---

# Unsafe

The root-level `unsafe` namespace is provided by ByteSkript and contains a number of basic functions and operations for controlling the system or providing useful functionality.

{% hint style="info" %}
The `unsafe` library is not imported by default, so functions must be run with the external `function(args) from unsafe` syntax.
{% endhint %}

This library is currently implemented in Java.

### Internal Control

The `unsafe` library contains some functions for monitoring or manipulating internal features, such as script processes and class loaders.

These are inherently dangerous and will never be added as library-level syntax, since there is no safe or reliable way for most users to interact with them.

### Trial Features

A rotating set of features may be trialled here before being added as full syntax. This gives users the opportunity to experiment with these features.

Not all trial features will move to the syntax development phase. Some may be discarded or replaced with an improved version later.

Some functions may be moved to the `skript` namespace if their purpose is valuable but they do not meet the requirements to become syntax.
