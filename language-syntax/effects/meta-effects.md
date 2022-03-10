---
description: >-
  Special effects with irregular behaviour, such as the ability to use other
  effects as parameters.
---

# Meta-Effects

Some effects have irregular structure or behaviour. These usually provide advanced behaviour, such as taking another effect as an input and modifying it.

### Inline Try

```clike
try[ to]: %Effect%
try to: assert false
try: run my_func()
```

This runs the provided effect in an implicit `try` section, ignoring any errors it produces.

There is no way to obtain the error produced by it (if one was) or to check whether it passed, other than checking for the result of the effect used.

{% hint style="info" %}
In most cases, the [try/catch section](../sections/try-catch.md) is a safer and more useful option.
{% endhint %}
