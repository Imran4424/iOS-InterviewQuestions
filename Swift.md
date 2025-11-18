# Swift Interview Questions

# Core Swift Basics

### What are the main differences between `let` and `var`?

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

### When do you use `guard let` vs `if let`?

#### `guard Let`

- Used to exit early if the condition fails.
- Keeps the unwrapped value in scope for the remainder of the function.

```swift
func process(middleName: String?) {
    print("Starting process...")

    // Ensure we have a middle name to proceed. If not, exit immediately.
    guard let name = middleName else {
        print("Error: Required middle name not provided. Exiting function.")
        return // MUST exit the scope
    }

    // The unwrapped 'name' variable is available from here until the end of the function.
    let fullMessage = "The middle name is: \(name)"
    print(fullMessage)

    // ... main logic continues without indentation ...

    print("Process finished successfully with name: \(name).")
}
```

#### `if let`

Used for branching logic; unwrapped value is only available inside the `if` block.

```swift
func process(middleName: String?) {
    print("Starting process...")

    // The unwrapped 'name' variable is only available inside these braces {}
    if let name = middleName {
        let fullMessage = "The middle name is: \(name)"
        print(fullMessage)
        // ... more operations using 'name' ...
    } else {
        print("No middle name provided.")
    }

    // You cannot use 'name' here.
    print("Process finished.")
}
```

### What is error handling?

Error handling is the process of responding to and recovering from errors or unexpected issues that occur during a program's execution to prevent it from crashing or failing unexpectedly.

#### Key components of error handling

- **Error detection:** Identifying when an error has occurred, such as a file not being found or a network request failing.
- **Error reporting:** Informing the user or system administrator about the error, often through error codes, logs, or user-friendly messages.
- **Error recovery:** Implementing a strategy to deal with the error, which could include:
  - **Retrying the operation:** Attempting the task again.
  - **Providing a fallback:** Using a default value or alternative option.
  - **Cleaning up resources:** Releasing any resources that were acquired, even if an error occurred (often using a `finally` clause).
  - **Isolating the failure:** Preventing a failing component from bringing down the entire system.


#### Benefits of proper error handling

- **Increased reliability:** Programs are more stable and less likely to crash.
- **Better user experience:** Users are less likely to be frustrated by unexpected failures.
- **Improved security:** Properly handling errors can prevent vulnerabilities that could be exploited by attackers.
- **Easier debugging:** Detailed error logs make it simpler for developers to find and fix problems. 

### How does Swift handle errors? What’s the difference between `try?`, `try!`, and `try`?

Swift handles errors using a system of throwing, catching, propagating, and manipulating recoverable errors at runtime.

Errors are represented by types that conform to the `Error` protocol, often using an `enum` for specific error conditions.

Functions that can produce an error are marked with the `throws` keyword in their declaration.

The `try`, `try?`, and `try!` keywords are used when calling a function that is marked as `throws` to specify how potential errors should be handled:

#### `try`

Error is propagated and must be handled by a surrounding `do-catch` statement or the calling function (if it's also a throwing function).

**When to  use**

When you need to handle specific errors and potentially recover from them, or when you want to provide user feedback based on the error.

#### `try?`

If an error is thrown, the result is `nil`; otherwise, the result is an optional containing the return value.

**When to  use**

When you don't care about the specific error that occurred and just want to know if the operation succeeded or not (e.g., in a `guard let` statement).


#### `try!`

Force unwraps the result; if an error is thrown, the app will crash with a runtime error.

**When to  use**

Only when you are absolutely certain the operation will not fail (e.g., loading an image from a guaranteed-to-exist asset in your app bundle). Use with extreme caution.

```swift
enum DataError: Error {
    case invalidPath
    case insufficientPermissions
}

func processData(path: String) throws -> String {
    // some operations that can throw DataError
    if path.isEmpty {
        throw DataError.invalidPath
    }
    return "Processed Data"
}

// Using 'try' with do-catch
do {
    let result = try processData(path: "some/path")
    print("Success: \(result)")
} catch DataError.invalidPath {
    print("Error: Invalid file path.")
} catch {
    print("An unexpected error occurred: \(error)")
}

// Using 'try?'
let optionalResult = try? processData(path: "") // optionalResult will be nil

// Using 'try!' (will crash if an error is thrown)
// let guaranteedResult = try! processData(path: "") 
```

# Automatic Reference Counting (ARC) & Memory Management

### What is Automatic Reference Counting(ARC) in Swift?

Automatic Reference Counting (ARC) is Swift's automated memory management system, used to track and manage the memory used by your app's class instances. In most cases, this means memory management "just works" and we don’t need to think about memory management ourself.

**How Automatic Reference Counting (ARC) works**

#### Reference Counting

Every time we create a new instance of a class, Automatic Reference Counting (ARC) allocates a chunk of memory to store information about that instance. This memory holds information about the type of the instance, together with the values of any stored properties associated with that instance and places a "strong reference" to it in a property, constant, or variable.

#### Tracking References

Automatic Reference Counting (ARC) keeps track of the number of strong references to each object. A strong reference prevents an instance from being deallocated.

#### Deallocation

As long as at least one strong reference to an instance exists, Automatic Reference Counting (ARC) will not deallocate it. When the last strong reference is removed (e.g., a variable is set to nil or goes out of scope), ARC automatically deallocates the instance's memory, freeing up resources.

#### Scope

Automatic Reference Counting (ARC) applies only to class instances (reference types). Value types like structures and enumerations are not managed by ARC; their memory is typically managed on the stack.






