---
description: Events run when a script is loaded.
---

# Loading Events

The core library contains two script loading events.

### Any Load

```clike
on any [script] load
on any script load:
    trigger:
        print event-name + " was loaded"
```

This is run whenever any script is loaded. This may be the current script or a different script. During initial start-up, scripts will not receive load events for other scripts loaded before them.

This is designed to catch the loading of 'lazy' scripts loaded while the program is already running.

<table><thead><tr><th>Event Value</th><th data-type="select">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>name</code></td><td></td><td>The name of the loaded script.</td></tr><tr><td><code>path</code></td><td></td><td>The full path of the loaded script in <code>skript/path/script</code> format.</td></tr></tbody></table>

### Load

```clike
on [script] load
on script load:
    trigger:
        print "this script was loaded"
```

This is run when **this** script is loaded. Other scripts will not receive this event.

This is designed to be a start-up entry-point for your program, equivalent to Java's `<clinit>`.

<table><thead><tr><th>Event Value</th><th data-type="select">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>name</code></td><td></td><td>The name of the loaded script.</td></tr><tr><td><code>path</code></td><td></td><td>The full path of the loaded script in <code>skript/path/script</code> format.</td></tr></tbody></table>

