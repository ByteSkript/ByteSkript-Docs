---
description: >-
  Supporting a specific file type at the language level, e.g. adding syntax to
  easily manipulate an object notation or for serialisation.
---

# Adding a File Type

Library developers are encouraged to add support for specific file-types at the language level.

An example of this is through the Skript config `.csk` file type.

### Creating a Special Matcher

In order to support the typical `path/to/file.ext` structure, we cannot use the normal pattern system.

In the constructor, we can specify a **dummy** pattern. This will <mark style="color:red;">not</mark> be used directly by the matcher.

```java
public MyFile() {
    super(LIBRARY, StandardElements.EXPRESSION, "path/to/file.ext");
}
```

Currently, this would match only the exact text `path/to/file.ext` but we need it to match any (valid) file-path ending in our `.ext` file extension.

By overriding the `match` method we can handle this behaviour ourselves.

```java
@Override
public Pattern.Match match(String thing, Context context) {
    if (!thing.endsWith(".ext")) return null; // null = no match
    if (thing.contains(" ")) { // no spaces in file-path, make sure we're not matching too much
        context.getError().addHint(this, "File paths should not contain spaces.");
        // this hint will let people know why it didn't match, IF no other syntax matched
        return null; // null = no match
    }
    return new Pattern.Match(Pattern.fakeMatcher(thing), thing);
    // the "fake matcher" creates an exact pattern for what the user entered
}
```

This can be broken down into **three** key sections.

1. We make sure the text is what we want (ends in our `.ext`, is a valid file path, etc.)
2. If it's wrong, we make sure the matcher fails.
3. If it's right, we return a fake "success" matcher.

### Handling the File

Since our pattern is non-standard we will need to handle the call manually.

This is done by overriding the `compile` method.

```java
@Override
public void compile(Context context, Pattern.Match match) throws Throwable {
}
```

The file-path will <mark style="color:red;">not</mark> be on the stack since it is not an input. Fortunately, it is available to add easily.

```java
@Override
public void compile(Context context, Pattern.Match match) throws Throwable {
    final MethodBuilder method = context.getMethod();
    // method = the current method we're writing
    final String path = match.<String>meta().trim();
    // path = the actual syntax path the user wrote
    method.writeCode(WriteInstruction.push(path));
    // we 'push' the `path` string onto the stack
}
```

So far, we have put only a string onto the stack, but we will likely want either a `File` object or some malleable handler object for the file that a user can edit.

Imagine, for example, this object is accessible via a `MyFile.get(String)` static method.

Having put our path string onto the stack we can now call this method directly, which will return the file object from our expression.

```java
@Override
public void compile(Context context, Pattern.Match match) throws Throwable {
    final String path = match.<String>meta().trim();
    final MethodBuilder method = context.getMethod();
    method.writeCode(WriteInstruction.push(path));
    
    final Method target = MyFile.class.getMethod("get", String.class);
    // we get the reflection handle for the method
    method.writeCode(WriteInstruction.invoke(target));
    // we tell the compiler to call that method
}
```
