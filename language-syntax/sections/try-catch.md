---
description: Handling expected errors and dealing with the result.
---

# Try / Catch

The `try/catch` section is designed to handle expected errors and prevent the program terminating, while also allowing special fail-case behaviour to be run.

It forms a tree structure of two possible branches. One or both of these may be run.

```clike
try:
    // attempted branch
catch %Variable%:
    // failure branch
```

### Try Block

These must **always** start with a `try` effect as the section header. This header has no inputs or options.

```clike
try:
    print "yes"
```

The code in this block will always be attempted. However, it is run with the expectation of failing (an error being thrown.)

The `try` section must **always** be followed by a `catch` section.

### Catch Block

These must **always** follow a `try` section. The `catch %Variable%` section accepts a normal variable.

The error will be stored in this variable.

```clike
try:
    print "always run"
catch {var}:
    print "caught the error: " + {var}
print "continues here"
```

The code in this block will be run **only** if the preceding try-section fails. If no error is thrown, the `catch` section will not be run.

The catch section will be jumped to after the line that fails

```clike
try:
    print "hello"
    assert 1 is 2 // error! jump -> catch
    print "never run" // this will not be reached.
catch {var}: // {var} contains the error object
    print "caught an error: " + {var}
print "continues here"
```

If no error occurs, the catch-section will be skipped.

```clike
try:
    print "always run"
catch {var}:
    print "never run"
print "continues here"
```

Any `break` instructions will exit this tree entirely (jump to after the `catch` section finishes.)

```clike
try:
    print "always run"
    break section // exits all
    print "never run"
catch {var}: // skipped by the break
    print "never run"
print "continues here"
```
