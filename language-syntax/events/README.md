---
description: Special program entry-points triggered when certain states are met.
---

# Events

The event system is used to run triggers when a certain program state is met.

Events are root-level [members](../members/).

Events are triggered automatically by the Skript implementation or by libraries.

### Language Rules

1. Events should never contain inputs.
2. Events should begin with `on`.

### Compilation

Each event trigger will compiled to its own unique method. This allows a script to have multiple handlers for the same event.

Each trigger will be started as an entry-point on its own script thread.&#x20;

All event triggers will receive the same `event` object and its values.
