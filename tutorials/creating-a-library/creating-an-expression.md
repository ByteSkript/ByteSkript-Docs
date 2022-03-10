---
description: Expressions are value-providing language elements that go inside a line.
---

# Creating an Expression

Most expressions will extend the `SimpleExpression` class, which deals with most of the basic functionality.

Other template classes exist for expressions with special behaviour.

{% code title="ExampleExpression.java" %}
```java
public class ExampleExpression extends SimpleExpression {
    
    public ExampleExpression(Library provider) {
        super(
            provider, // your library instance
            StandardElements.EXPRESSION, // the syntax type
            "pattern goes here" // the pattern(s)
        );
    }
    
}
```
{% endcode %}

Most expressions will use the `StandardElements.EXPRESSION` enum. This tells the compiler what kind of language element it is, so that other syntax can filter it properly.

### Adding Behaviour

The syntax class does not necessarily need to provide the functionality it promises. For simple things, methods can be specified in the `handlers` map for different states.

Below is a very simple example expression that returns the system line separator string.

{% code title="ExampleExpression.java" %}
```java
public class ExampleExpression extends SimpleExpression {
    
    public ExampleExpression(Library provider) {
        super(provider, StandardElements.EXPRESSION, "example expression");
        this.handlers.put(
            StandardHandlers.GET, // this is used when in GET mode
            findMethod(System.class, "lineSeparator") // System.lineSeparator()
        );
    }
    
}
```
{% endcode %}

The `GET` handler provides behaviour for when this expression is being 'retrieved' such as being used in another expression or effect. This handler is the most common.

Expressions that support the `set...` and `delete...` effects would register handlers for `SET` and `DELETE` as well.

In this example, when the `GET` mode is used, the `System.lineSeparator()` method will be called.

### Dealing with Inputs

Lots of expressions will have inputs where a particular `%Type%` is required. The user provides the value by putting another syntax in this slot.

These inputs will be put directly into the parameter slots of the handler method.

{% code title="ExampleExpression.java" %}
```java
public class ExampleExpression extends SimpleExpression {
    
    public ExampleExpression(Library provider) {
        super(provider, 
            StandardElements.EXPRESSION, 
            "example %String% expression %Number%"
        );
        this.handlers.put(
            StandardHandlers.GET, // this is used when in GET mode
            findMethod(ExampleExpression.class, "myMethod", String.class, Number.class)
        );
    }
    
    public static String myMethod(String string, Number number) {
        return string + ", " number;
        // "hello" 6 -> "hello, 6"
    }
    
}
```
{% endcode %}

For this example, when running the syntax `example "hello" expression 6` the script would directly call our `myMethod` method from the class with those arguments.

{% hint style="danger" %}
Our method may not be available at runtime! See the [#syntax-availability](creating-an-expression.md#syntax-availability "mention") section for more details.
{% endhint %}

The arguments are **always** passed in order.

{% hint style="info" %}
A user can give incorrect types to a syntax (e.g. through a variable) so it is recommended to make the parameters `Object` and check (or convert) their type where possible.

This is not required, and there may be situations where you do not want to handle this.
{% endhint %}

### Return Types

Lastly, we ought to specify what type this expression returns (so that the syntax its used in knows what to expect.)

{% hint style="info" %}
This is specified as a `Type` rather than a `Class<?>` since the type may not exist yet (if it is a custom type, for example.)&#x20;

A `Type` can be created with `new Type(class)`.
{% endhint %}

Common types (string, object, etc.) can be found in the `CommonTypes` class to avoid multiple of the same type object being created.

{% code title="ExampleExpression.java" %}
```java
public class ExampleExpression extends SimpleExpression {
    
    public ExampleExpression(Library provider) {
        super(provider, StandardElements.EXPRESSION, "example expression");
        handlers.put(StandardHandlers.GET, findMethod(System.class, "lineSeparator"));
    }
    
    @Override
    public Type getReturnType() {
        return CommonTypes.STRING;
    }
    
}
```
{% endcode %}

Our expression class will then need to be registered with our Library. If we are using the ModifiableLibrary template, this can be done in the library's constructor.

```java
public MyLibrary() {
    super("my_library");
    registerSyntax(CompileState.STATEMENT, new ExampleExpression(this));
}
```

### Compile States

Compile states tell the compiler where your syntax is allowed to be used. For expressions, this will almost always be in a **statement** (e.g. inside an effect.)

### Syntax Availability

If the `SkriptCompiler` or `SkriptJarBuilder` tools are used, our syntax class will not be available in the output. As it requires our `doSomething` method, this syntax will **not** be usable at runtime.

{% hint style="success" %}
This is done so that result-jars can be minified and run as a standalone program without needing the libraries to be packaged in.
{% endhint %}

Obviously, we need our syntax to work, so we have three options.

#### Use a Runtime-present Class

Firstly, we can call a method from a class that **is** available at runtime.

Our library may specify runtime dependencies to be exported to the compiled Jar file. We could call a method from one of those classes rather than a syntax class.

#### Extract the Method

Secondly, we can tell the ByteSkript compiler to **export** our method into the compiled script.

This can be done using the `@ForceExtract` annotation.

```java
@ForceExtract
public static String doSomething(Object input) {
    return input + " blob"
}
```

The compiler will extract the bytecode source of our method and add it to the script's class as a hidden **synthetic** method, which will be called instead.

This means we don't need any runtime dependencies for our syntax at all.

{% hint style="danger" %}
This is not suitable for all methods.

* If your method contains a lambda this cannot be exported (the dynamic instruction uses a local hidden class.)
* If your method accesses a local field this cannot be exported.
* If your method calls another local method this cannot be exported.
{% endhint %}

#### Inline the Method

Thirdly, we can tell the ByteSkript compiler to **inline** our method directly into the compiled script

This can be done using the `@ForceInline` annotation.

```java
@ForceInline
public static String doSomething(Object input) {
    return input + " blob"
}
```

The compiler will extract the bytecode source of our method and attempt to convert it and write it directly into the compiled script's code without a method call.

This means we don't need any runtime dependencies for our syntax.

{% hint style="danger" %}
This is a **very dangerous** extraction. It should not be used unless you understand how to balance stack frames.

Very few methods are suitable for inlining.

* They cannot contain non-trivial jumps.
* They cannot contain switches/throws.
* They cannot contain more than one return path.

This is designed for very precise instructions where inlining would give an efficiency bonus.
{% endhint %}
