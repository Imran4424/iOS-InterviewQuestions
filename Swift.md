# Swift Interview Questions

## Core Swift Basics

#### What are the main differences between let and var?

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