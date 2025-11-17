# Swift Interview Questions

# Core Swift Basics

### What are the main differences between let and var?

- `let` → constant binding: value cannot be reassigned after initialization.
- `var` → variable binding: value can be reassigned.
  
For reference types (classes), `let` means the reference cannot change, but the object’s properties can still mutate if they’re declared with `var`.

```swift
class Person {
    let name: String = "Alice" // Immutable property
    var age: Int = 30           // Mutable property
}

let alice = Person() // The 'alice' reference is constant

// This is valid, as 'age' is a 'var' property within the object:
alice.age = 31

// This causes a compile-time error, as 'name' is a 'let' property:
// alice.name = "Bob"

// This causes a compile-time error, as 'alice' is a 'let' reference:
// alice = Person()
```

For value types (like struct, enum, and tuples), the `let` keyword makes the entire instance immutable, along with all of its properties.

```swift
struct Coordinate {
    var x: Int // Declared as 'var' within the struct
    var y: Int // Declared as 'var' within the struct
}

let point = Coordinate(x: 10, y: 20) // The 'point' instance is constant

// This causes a compile-time error:
// point.x = 15
// error: cannot assign to property: 'point' is a 'let' constant
```

### What are value types vs reference types in Swift?

- Value types: `struct`, `enum`, `Tuple`. Copied on assignment and when passed to functions.
- Reference types: `class`, `actor`, `closures`. Passed by reference; multiple references point to the same instance.

For iOS, using value types helps avoid unintended shared mutable state and simplifies reasoning about concurrency.

### What is the difference between struct and class?

Key Differences:

- `struct` is a value type, `class` is a reference type.
- `class` supports inheritance; `struct` doesn’t(only through protocols).
- `class` supports deinitializers (deinit) and reference counting; `struct` doesn’t.
- `class` instances can be used with Objective-C runtime (@objc, dynamic) when appropriate.
- `class` is the base of Object Oriented Programming(OOP), `struct` is base of Protocol Oriented Programming(POP)

# Optionals & Error Handling

### What is an optional in Swift and why is it useful?

An Optional in Swift is a type that safely handles the absence of a value. An optional variable can hold one of two states:

1. It contains a value of a specific type (e.g., an `Int` or a `String`)
2. It contains `nil`, meaning there is no value present.

Optionals are not pointers; they are essentially an enumeration with two cases, `some(Value)` or `none`, which the compiler uses to enforce safety

- Type: `T?` means “either a value of type `T` or `nil`”
- Forces you to handle absence of a value explicitly, reducing runtime crashes like “null pointer exceptions”

We can unwrapped optional values using `if let`, `guard let`, optional chaining, nil coalescing operator `??`, or (carefully) `!`.


