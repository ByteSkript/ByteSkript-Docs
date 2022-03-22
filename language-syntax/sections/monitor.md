---
description: Used to ensure safe and proper access to resources over multiple processes.
---

# Monitor

The `monitor` section is an advanced effect designed to manage resources that are used across multiple processes.

Variable types are designed to be thread-safe, so that changes to their values can be seen by all threads. However, multiple processes can simultaneously try to read and modify the same data.

```clike
set {@var} to 5
run a new runnable in the background:
    add 1 to {@var}
    assert {@var} is 6 // may error
assert {@var} is 6 // may error
remove 1 from {@var}
```

In this example, either of the `assert` checks could fail: both processes will run simultaneously and could modify/read the atomic variable in any order.

Two possible outcomes are shown below.

```
Thread 1: set {@var} to 5
Thread 1: assert {@var} is 6 // ERROR - process ends here
Thread 2: add 1 to {@var}
Thread 2: assert {@var} is 6 // passes
```

```
Thread 1: set {@var} to 5
Thread 2: add 1 to {@var}
Thread 1: assert {@var} is 6 // passes
Thread 1: remove 1 from {@var}
Thread 2: assert {@var} is 6 // ERROR - process ends here
```

{% hint style="danger" %}
This is known as a **race condition**: the execution order of the two processes will determine the outcome of the program.
{% endhint %}

Race conditions like these are often random and unpredictable, despite the background process usually being slower to start. Tiny variations in the soft or solid state of the machine can impact the order of outcomes.

In order to prevent this we can put a **monitor** on a resource that prevents other processes from accessing it until our one finishes. The monitor will also make sure any previous changes are finished and closed before our code runs.

{% hint style="info" %}
A monitor locks a particular object, not its variable.

For example, we cannot lock a numerical variable `{var} = 5` because the value `5` will change.
{% endhint %}

```clike
set {@var} to 5
set {lock} to a new object // a dummy object we can use to handle our lock
monitor {lock}:
    run a new runnable in the background:
        monitor {lock} // process waits here until the other lock finishes
        assert {@var} is 6 // the change is guaranteed to have finished
    assert {@var} is 5 // no other access has happened yet
    add 1 to {@var}
// monitor unlocks, background process can continue
```

This can be used to create **reliable** programs that share resources across multiple processes.

{% hint style="warning" %}
Processes will pause until a resource is unlocked. Be careful with locking resources that are used by a sub-process since this can deadlock the program if the lock is never released.
{% endhint %}

&#x20;
