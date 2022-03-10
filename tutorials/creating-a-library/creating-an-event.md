---
description: A program entry-point, which scripts can "listen" to from multiple triggers.
---

# Creating an Event

Events are an **entry-point** for a program: they always start a **new** process.

The event object is available from anywhere within the process it starts via the `event` expression. Events will usually register special literal properties (event-values) for scripts to read from.

### Event Syntax

The event syntax handler should extend the `EventHolder` class.

```java
public class MyEventSyntax extends EventHolder {
    
    public MyEventSyntax() {
        super(myLibrary, "on my event");
    }
    
    @Override
    public Class<? extends Event> eventClass() {
        return MyEvent.class; // this is the actual event class
    }
    
}
```

{% hint style="success" %}
Event syntax should **always** start with `on` for the sake of clarity.
{% endhint %}

### Event Object

Most of the behaviour is handled within the event object. This is a **separate** class from the event syntax handler, and an instance is given to Skript in order to trigger the event.

```java
public class MyEvent extends Event {
    
    public MyEvent() {
    }
    
    @EventValue("name") // event-name
    public String getName() {
        return script.getSimpleName();
    }
    
    @EventValue("number") // event-number
    public Number getNumber() {
        return 10.5;
    }
    
}
```

### Triggering the Event

The event can be triggered from the Skript instance.

```java
skript.runEvent(new MyEvent());
```

It may also be run only for an individual script.

```java
skript.runEvent(new MyEvent(), script);
```

Event triggers will always start a new process (or recycle a finished one where possible) but multiple event triggers called at the same time will receive the same event object, so be careful with concurrency-sensitive data.

{% hint style="info" %}
The script process can be retrieved from within an event-value method, since it is guaranteed to be run on a Skript thread.
{% endhint %}
