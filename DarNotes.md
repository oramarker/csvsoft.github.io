### Dart

#### Concept

* All variables reference to objects;

#### Built-in Special Types

- `Object`: The superclass of all Dart classes except `Null`.

- `Never`: Indicates that an expression can never successfully finish evaluating. Most often used for functions that always throw an exception.
- `dynamic`: Indicates that you want to disable static checking. Usually you should use `Object` or `Object?` instead.
- `void`: Indicates that a value is never used. Often used as a return type.



A *closure* is a function object that has access to variables in its lexical scope, even when the function is used outside of its original scope.



Dart programs can throw any non-null object—not just Exception and Error objects—as an exception.



### Class

### Implicit Interface

Every class implicitly defines an interface containing all the instance members of the class and of any interfaces it implements

#### constructors

Use the `factory` keyword when implementing a constructor that doesn’t always create a new instance of its class. For example, a factory constructor might return an instance from a cache, or it might return an instance of a subtype.



### Generics

Dart generic types are *reified*, which means that they carry their type information around at runtime.



## Library visibility

identifiers that start with an underscore (`_`) are visible only inside the library. *Every Dart app is a library*, even if it doesn’t use a `library` directive.



### Async

An `async` function is a function whose body is marked with the `async` modifier.

