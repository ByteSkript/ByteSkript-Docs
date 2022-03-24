---
description: >-
  Useful information and instructions for contributing to the development of
  ByteSkript.
---

# Contributing to ByteSkript

As an open-source project, ByteSkript welcomes community involvement.

There are three main ways in which you can contribute to the project:

1. **Improving documentation and user resources.**\
   You can help users by creating more examples, information and tutorials for our documentation.\
   If you want to make significant contributions to the documentation you may join our organisation.\
   You can also write test code (in Skript) to help catch bugs before releases.
2. **Contributing to the compiler/runtime source code.**\
   You can create pull-requests on the [GitHub](https://github.com/Moderocky/ByteSkript) with contributions to our source code.\
   You could add or improve syntax, internal features or add extra tests written in Skript to help catch bugs before releases.\
   Make sure to check the [to-do list](https://github.com/Moderocky/ByteSkript/blob/master/TODO.md) for most-wanted features.
3. **Creating DSL versions and language libraries.**\
   Rather than contributing to ByteSkript itself, you can create [language libraries](creating-a-library/) to add syntax and function packages.\
   You can also extend ByteSkript to use as a domain-specific language in projects or for existing domains. This gives users more opportunities to use the language.

### Code Conventions

In most cases, ByteSkript follows '[Stylezie](https://github.com/Moderocky/Stylezie),' which is fairly similar to the Google Java style guide. Spacing and ordering of elements is less important - everything is automatically reformatted during the release process. If in doubt, follow the style of an existing element.

The exception to the style conventions is when working with runtime dependencies for the `skript` namespace. Although these are (currently) implemented in Java, they are designed to match and interact directly with Skript code, hence why the method names are in `lower_camel_case` and the class is in the root package in `lowercase`. This namespace will be retroactively implemented in Skript itself.

Methods in test classes should mirror the name of the function they call, and so may be in `lower_camel_case`.

All other Java content should follow [Stylezie](https://github.com/Moderocky/Stylezie).

### Contributing to the Source

There are three main areas to contribute to in the ByteSkript source.

The **parser** handles interpreting the language, matching it to the correct `SyntaxElement` and converting it to an `ElementTree` that can be used.

This uses fairly basic Java, but contributors may wish to familiarise themselves with pattern-matching.

The **compiler** handles converting the file and syntax `ElementTree` into VM [bytecode](https://en.wikipedia.org/wiki/Java\_bytecode) that the JVM is able to run. Significant contributions to this will require an understanding of the [virtual machine instruction set](https://docs.oracle.com/javase/specs/jvms/se17/html/jvms-6.html) and the class file format.

The [Foundation](https://github.com/Moderocky/Foundation) library is available for simple instructions, but complex behaviour will require using [ASM](https://asm.ow2.io) directly, which is the same compiler framework used by the JDK internally.

The **runtime** involves manipulating threads and passing resources to scripts. This involves fairly basic Java, although some of the class-loading and thread manipulation is more complex.
