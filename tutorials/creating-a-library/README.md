---
description: >-
  A basic guide to creating an external language library (addon) that provides
  extra syntax or functionality.
---

# Creating a Library

The default ByteSkript compiler supports external language libraries which can add new syntax, language features and even runtime dependency classes that will be exported when using the **SkriptJarBuilder**.

These libraries can be written in Java or other JVM languages, using the ByteSkript API.

### Dependency Information

When using Maven, ByteSkript can be added as a dependency with the details below. Remember to change the dependency version to the latest [release tag](https://github.com/Moderocky/ByteSkript/tags), since this example will not be updated.

ByteSkript's repository also houses all of its dependencies, in case these are needed as well.

```xml
<repository>
    <id>kenzie</id>
    <name>Kenzie's Repository</name>
    <url>https://repo.kenzie.mx/releases</url>
</repository>


<dependency>
    <groupId>org.byteskript</groupId>
    <artifactId>byteskript</artifactId>
    <version>1.0.24</version>
    <scope>provided</scope>
</dependency>
```

For Gradle, the details are below. Again, make sure to check you are using the latest [release tag](https://github.com/Moderocky/ByteSkript/tags).

```groovy
repositories {
    byteskript {
        url "https://repo.kenzie.mx/releases"
    }
}

dependencies {
    byteskript 'org.byteskript:byteskript:1.0.24'
}
```

### Basic Library Structure

All libraries need a core class implementing the `org.byteskript.skript.api.Library` interface. An instance of this class will be registered and will provide the syntax and features.

Libraries also need a **main** class. This has to be specified in the `MANIFEST.MF`.

These can be the same class for convenience.

The main class needs a `public static void load(Skript skript)` method.

For simplicity, you can extend the `ModifiableLibrary` class.

{% code title="MyLibrary.java" %}
```java
package org.example;

import org.byteskript.skript.api.*;
import org.byteskript.skript.runtime.Skript;


public class MyLibrary extends ModifiableLibrary implements Library {
    
    public MyLibrary() {
        super("my_library");
    }
    
    public static void main(String[] args) {
    
    }
    
    // This method will be called by ByteSkript
    public static void load(Skript skript) {
        skript.registerLibrary(new MyLibrary()); // simple registration
    }
    
}
```
{% endcode %}

The `Library` interface has several important methods you will need to implement if not using the `ModifiableLibrary` class.

| Method          | Description                                                                                                                                                                             |
| --------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `name`          | Your library's name (for error messages, etc.)                                                                                                                                          |
| `getTypes`      | An array of types that your library wants to use for `%Type%` syntax patterns.                                                                                                          |
| `getConstructs` | <p>An array of language constructs registered by your library. Basic libraries will not register any.<br>This can be used to add entirely new language grammar.</p>                     |
| `getSyntax`     | <p>An array of syntax classes registered by your library.<br>This is where you register new syntax.</p>                                                                                 |
| `getProperties` | A list of properties registered by your library. This is for adding complex property expressions. This is for use with the v2 syntax API.                                               |
| `getHandlers`   | <p>Returns a list of handlers valid for the current state and language element for filtering purposes.<br>This may simply be a version of <code>getSyntax</code> filtered by state.</p> |

The `org.byteskript.skript.api.ModifiableLibrary` class can be extended to provide a basic Library implementation, where syntax can be manually registered in the constructor. This will mean you do not need to implement the methods.

ByteSkript's built-in `SkriptLangSpec` uses the `ModifiableLibrary` template internally.

{% hint style="info" %}
**SkriptLangSpec** is the built-in implementation of the Skript language.

A modified version of ByteSkript could use a custom language implementation with the default compiler.
{% endhint %}

### Registering a Simple Syntax

Most libraries will want to register syntax. This can be done using either the v1 or the v2 API. Examples in this section are for the v1 API, which allows greater control over registration.

#### Advanced Syntax

