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

**Resolving Strong Reference Cycles**

While Automatic Reference Counting (ARC) handles memory automatically in most scenarios, a problem can arise with **strong reference cycles (also known as retain cycles)**. A strong reference cycle occurs when two or more objects hold strong references to each other, preventing their reference counts from ever reaching zero and causing a **memory leak**.

To resolve these, Swift provides two alternative reference types:

#### Weak References

Declared with the `weak` keyword, a weak reference does not keep a strong hold on the instance it refers to, so it doesn't prevent deallocation. A weak reference must be an optional type because the referenced object can be deallocated and the reference automatically set to `nil` at any time. Use weak references when the other instance has a shorter lifetime.

#### Unowned References

Declared with the `unowned` keyword, an unowned reference also does not keep a strong hold on the instance. Unlike a weak reference, an unowned reference is a non-optional type and is used when you are certain the referenced instance will always have a value and has the same or a longer lifetime than the referencing object. Accessing an unowned reference after its instance has been deallocated will cause a runtime error.

### Strong References vs `weak` References vs `unowned` Reference

#### Strong Reference

A strong reference is the default type of reference in Swift. When you create a property, constant, or variable that refers to a class instance, it is a strong reference by default.

**Function:** A strong reference increments the instance's reference count based on its usage count. As long as there is at least one strong reference to an object, ARC will not deallocate it.

**Purpose:** This ensures that objects required by your program remain in memory as long as they are needed.

**Caution:** Strong references can lead to strong reference cycles (retain cycles) if two objects strongly refer to each other, causing a memory leak because neither object's reference count can reach zero.

```swift
class Person {
    let name: String
    
    init(name: String) {
        self.name = name
        print("\(name) is being initialized")
    }

    deinit {
        print("\(name) is being deinitialized")
    }
}
```

#### `weak` References

A weak reference is used to solve strong reference cycles.

**Function:** A weak reference does not increment the instance's reference count based on its usage count.

**Characteristics:**
- It must be declared as a variable (`var`) because its value needs to be changeable (set to `nil`).
- It must be an `Optional` type because the object it points to might be deallocated at any time, in which case the reference automatically becomes `nil`. 

**Purpose:** Use weak references when the referenced object can be deallocated independently, typically for a relationship where one object "owns" the other but the "owned" object does not need to own its "owner" back (e.g., a `Person` might strongly own a CreditCard, but the `CreditCard` should only weakly reference the `Person`).

#### `unowned` References

An `unowned` reference is also used to solve strong reference cycles in specific situations.

**Function:** Like a weak reference, it does not increment the instance's reference count.

**Characteristics:**
- It is used when you know the reference will never be nil once it has been set.
- It is declared as a non-optional type.
- Attempting to access an unowned reference after the object it points to has been deallocated will cause a runtime crash.

**Purpose:** Use unowned references when the other instance has the same or a longer lifespan. A common use case is a closure capturing `self` where `self` and the closure always deallocate at the same time.

| Reference Type | Contributes to Reference Count? | Optional? | Behavior When Object is Deallocated | When to Use | 
| -------------- | -------------- | -------------- | -------------- | -------------- | 
| Strong | Yes | No | Prevents deallocation; object remains in memory. | The default. Use for standard ownership where you need the object to persist.|
| `weak` | No | Yes (must be `Optional`) | Automatically sets the reference to `nil` when the object is deallocated. | When the referenced object has a shorter or independent lifespan. Used to break retain cycles. |
| `unowned` | No | No (non-optional) | Causes a runtime crash if accessed after the object is deallocated. | When the referenced object has the same or a longer lifespan. Used to break retain cycles when the reference is guaranteed to always be valid. | 


### What is a retain cycle? How do you break it?

A retain cycle happens when two (or more) reference types hold strong references to each other, so their reference counts never reach zero.

```swift
class Person {
    let name: String
    var apartment: Apartment?

    init(name: String) { 
        self.name = name
        print("\(name) is being initialized")   
    }

    deinit { 
        print("\(name) is being deinitialized") 
    }
}

class Apartment {
    let unit: String
    var tenant: Person?

    init(unit: String) { 
        self.unit = unit 
    }
    
    deinit { 
        print("Apartment \(unit) is being deinitialized") 
    }
}

var john: Person?
var unit4A: Apartment?

john = Person(name: "John Appleseed")
unit4A = Apartment(unit: "4A")

john!.apartment = unit4A
unit4A!.tenant = john
```


![RetainCycle](StrongReference_Dark.png)