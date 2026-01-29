# Swift OOPs Notes

<a name="top"></a>

[<-- back](../README.md)

## Table of Contents
- [Core Relationships](#core-relationships)
- [Value vs Reference Semantics](#value-vs-reference-semantics)
- [Classes](#classes)
- [Structs](#structs)
- [Enums](#enums)
- [Protocols](#protocols)
- [OOPs Concepts in Swift](#-oops-concepts-in-swift)

---

## Core Relationships

### is-a relationship
The **is-a relationship** represents inheritance or subclassing, where one type is a specialized form of another. This means that a subclass inherits properties and methods from its superclass, establishing a parent-child relationship. For example, a `Car` is-a `Vehicle`.

```swift
class Vehicle {
    func move() {
        print("Vehicle is moving")
    }
}

class Car: Vehicle {
    func honk() {
        print("Car is honking")
    }
}

let myCar = Car()
myCar.move()  // Inherited method
myCar.honk()  // Subclass-specific method
```

### has behaviour of
The **has behaviour of** relationship describes protocol conformance, where a type adopts specific capabilities or behaviors by conforming to a protocol. This allows types to share functionality without inheritance.

```swift
protocol Flyable {
    func fly()
}

struct Bird: Flyable {
    func fly() {
        print("Bird is flying")
    }
}

let sparrow = Bird()
sparrow.fly()
```

---

## Value vs Reference Semantics

| Concept | Reference Semantics | Value Semantics |
| --- | --- | ---|
| Used by | class, actor, closure | struct, enum, tuple |
| How it works | Variables reference the same memory instance | Each variable gets its own independent copy | 
| Storage location | Heap (managed by ARC) | Stack (auto-managed) | 
| Mutation behavior | Changing one reference changes all (shared instance) | Changing one copy doesn’t affect others | 
| Performance | Slightly slower due to heap allocation and ARC | Faster — lightweight, no ARC | 
| Thread safety | Not thread-safe (shared mutable state) | Thread-safe (each copy isolated) | 

---

## Classes

Classes in Swift are reference types, stored in the heap, and managed by Automatic Reference Counting (ARC).  
They support inheritance, polymorphism, and reference semantics, unlike structs.
```swift
class Vehicle {
    var name: String
    init(name: String) {
        self.name = name
    }
}
```

When you assign one class instance to another variable, both refer to the same memory instance.
```swift
let car1 = Vehicle(name: "Tesla")
let car2 = car1
car2.name = "BMW"
print(car1.name) // "BMW" — both reference the same object
```

### Memory Management

Swift uses ARC (Automatic Reference Counting) to track and manage memory usage of class instances.  
Each object has a reference count — when it reaches zero, the object is deallocated.  
- When you create a new instance → reference count = 1
- When you assign another variable → reference count increases
- When a variable goes out of scope or set to nil → reference count decreases
- When count = 0 → ARC automatically frees memory

#### referencing
There are three types of reference ownership in Swift:

1. `strong`:  
    The default reference type.
    It increases the reference count and keeps the object alive as long as at least one strong reference exists.
    ```swift
    class Person {
        var pet: Pet?
    }
    class Pet {
        var owner: Person?
    }

    let john = Person()
    let dog = Pet()
    john.pet = dog       // strong reference
    dog.owner = john     // strong reference ❌ causes retain cycle
    ```

2. `weak`:
    A non-owning reference that does not increase the reference count.  
    It is automatically set to nil when the referenced object is deallocated.  
    Used to break retain cycles, typically in delegate patterns.
    ```swift
    class Owner {
        var pet: Pet?
    }
    class Pet {
        weak var owner: Owner?  // weak reference
    }

    var john: Owner? = Owner()
    var dog: Pet? = Pet()

    john?.pet = dog
    dog?.owner = john

    john = nil    // Owner deallocated
    print(dog?.owner)  // nil ✅
    ```

3. `unowned`:
    A non-owning reference that does not become nil automatically.  
    Used when you’re sure the referenced object will always exist during the lifetime of the reference.
    ```swift
    class CreditCard {
        unowned let customer: Customer
        init(customer: Customer) {
            self.customer = customer
        }
    }
    class Customer {
        var card: CreditCard?
        init() {
            card = CreditCard(customer: self)
        }
    }
    ```

---

## Structs

Structs in Swift are **value types**, meaning each instance keeps a unique copy of its data. They are stored on the stack, making them lightweight and efficient. Because each copy is independent, structs are inherently thread-safe, avoiding issues with shared mutable state.

Example demonstrating value semantics:

```swift
struct Point {
    var x: Int
    var y: Int
}

var p1 = Point(x: 0, y: 0)
var p2 = p1  // p2 is a copy of p1
p2.x = 10

print(p1.x)  // Outputs 0
print(p2.x)  // Outputs 10
```

---

## Enums

Enums in Swift are value types that represent a finite set of related cases. They can also have associated values to store additional information. Enums are useful for modeling state or choices in a type-safe way.

Example with associated values and switch-case:

```swift
enum NetworkResponse {
    case success(data: String)
    case failure(error: String)
    case loading
}

let response = NetworkResponse.success(data: "User data loaded")

switch response {
case .success(let data):
    print("Success with data: \(data)")
case .failure(let error):
    print("Failure with error: \(error)")
case .loading:
    print("Loading...")
}
```

---

## Protocols

### Overview
Protocols define blueprints of methods, properties, and other requirements that suit a particular functionality.  
They allow abstraction, composition, and code reuse without inheritance — enabling both value and reference types to adopt common behaviors.  

Protocols help achieve “has behavior of” relationships (e.g., Duck has behavior of Flyable).  
```swift
protocol Flyable {
    func fly()
}

struct Bird: Flyable {
    func fly() {
        print("Bird flying high 🕊️")
    }
}
```

---

### Default Implementation
Protocols can provide default implementations of methods using extensions.  
This allows conforming types to inherit behavior automatically, reducing code duplication.  
```swift
protocol Greetable {
    func greet()
}

extension Greetable {
    func greet() {
        print("Hello from default implementation 👋")
    }
}

struct Person: Greetable {}
Person().greet() // Uses default greet()
```
If the conforming type provides its own implementation, that one takes precedence over the default.

---

### Static vs Dynamic Dispatch
Default implementations in protocol extensions use static dispatch,
which means the compiler decides at compile time which function to call — not at runtime.
```swift
protocol Speaker {
    func speak()
}

extension Speaker {
    func speak() { print("Default speaking") }
}

class Human: Speaker {
    func speak() { print("Human speaking") }
}

let obj: Speaker = Human()
obj.speak() // ❗️ Prints "Default speaking" (static dispatch)
```
This happens because Swift statically binds calls to protocol extensions,
unless the method is defined in the protocol requirement itself.  

To enable dynamic dispatch, ensure the method is declared inside the protocol, not just in the extension.

---

### Protocol Inheritance
Protocols can inherit from other protocols, allowing composition and specialization of requirements.
```swift
protocol Movable {
    func move()
}

protocol Runnable: Movable {
    func run()
}

struct Dog: Runnable {
    func move() { print("Dog moving") }
    func run() { print("Dog running fast 🐕") }
}
```

---

### Diamond Problem
Unlike multiple inheritance in classes, protocol inheritance doesn’t cause a diamond problem.  
That’s because protocols don’t store state — they only define behavior.
When multiple protocols provide the same method name, Swift uses static dispatch to resolve ambiguity.  

If two protocol extensions define the same default implementation,
you can disambiguate by explicitly casting the type:
```swift
protocol A { func show() }
extension A { func show() { print("A") } }

protocol B { func show() }
extension B { func show() { print("B") } }

struct Test: A, B {}

let t = Test()
(t as A).show() // "A"
(t as B).show() // "B"
```

---

### Delegation
Delegation is a design pattern where one type delegates part of its responsibility to another type through a protocol.  
It’s widely used in UIKit (e.g., UITableViewDelegate, UICollectionViewDelegate).
```swift
protocol TaskDelegate: AnyObject {
    func didCompleteTask()
}

class Worker {
    weak var delegate: TaskDelegate?
    
    func startWork() {
        print("Work started...")
        delegate?.didCompleteTask()
    }
}

class Manager: TaskDelegate {
    func didCompleteTask() {
        print("Manager: Good job! ✅")
    }
}

let worker = Worker()
let manager = Manager()
worker.delegate = manager
worker.startWork()
```

---

### Class-only Protocols
Sometimes you want a protocol to be adopted only by classes,
especially when using weak references (since only class instances can be weak).  

Use AnyObject constraint:
```swift
protocol Trackable: AnyObject {
    func track()
}

class Tracker: Trackable {
    func track() { print("Tracking activity 📍") }
}
```
If you try to apply Trackable to a struct or enum → ❌ compile-time error.

---

### Generic Protocols and Type Erasure
Protocols with associated types make them generic by nature.
They can’t be used as concrete types directly because the associated type must be known at compile time.
```swift
protocol Container {
    associatedtype Item
    func add(_ item: Item)
}

// ❌ Error: Protocol 'Container' can only be used as a generic constraint
func printContainer(_ c: Container) {}
```
#### solution to make a generic protocol as concrete
To use such protocols as concrete types, you can use type erasure —
wrap the generic protocol in a concrete struct or class.

Example:
```swift
protocol AnyContainer {
    associatedtype Item
    func add(_ item: Item)
}

class Box<T>: AnyContainer {
    func add(_ item: T) {
        print("Added \(item)")
    }
}

// ✅ Using type erasure
struct AnyBox {
    private let _add: (Any) -> Void
    init<T: AnyContainer>(_ base: T) {
        _add = { item in
            if let casted = item as? T.Item {
                base.add(casted)
            }
        }
    }
    func add(_ item: Any) { _add(item) }
}

let intBox = Box<Int>()
let anyBox = AnyBox(intBox)
anyBox.add(42)
```
This pattern (known as type erasure) allows using generic protocols like normal types.

---

### Protocol Composition
You can combine multiple protocols into one requirement using the protocol composition operator &.
```swift
protocol Flyable { func fly() }
protocol Swimmable { func swim() }

struct Duck: Flyable, Swimmable {
    func fly() { print("Duck flying") }
    func swim() { print("Duck swimming") }
}

func performAction(on animal: Flyable & Swimmable) {
    animal.fly()
    animal.swim()
}

let donald = Duck()
performAction(on: donald)
```
This enables composition over inheritance,
allowing types to adopt multiple independent behaviors flexibly.




---

## 🧠 static vs dynamic dispatch
Swift uses both static and dynamic dispatch, depending on the type and how the method is declared.
Dynamic Dispatch:
- Happens at runtime
- The exact method to call is determined based on the object’s actual type
- Enables polymorphism

Static Dispatch:
- Happens at compile time
- The compiler knows exactly which method to call
- Faster (no runtime lookup)

Dispatch Rules in Swift
Static Dispatch	Dynamic Dispatch
Resolved at compile time	Resolved at runtime
Used by structs, enums, final methods, static methods, free functions	Used by class methods that can be overridden
Used by protocol extension–only methods	Used by protocol requirements
No runtime lookup (no vtable / witness table)	Uses vtable (classes) or witness table (protocols)
Faster	Slightly slower
Example: Dynamic Dispatch with Classes
class Animal {
    func speak() { print("Animal sound") }
}

class Dog: Animal {
    override func speak() { print("Bark") }
}

let pet: Animal = Dog()
pet.speak() // Dynamic dispatch → "Bark"


✔️ Even though pet is typed as Animal, Swift calls Dog.speak()
✔️ This happens via vtable lookup

Structs and Enums

Do not support inheritance

Cannot override methods

Always use static dispatch

struct Cat {
    func speak() { print("Meow") }
}

let c = Cat()
c.speak() // Static dispatch

Important Swift-Specific Notes (Add This 👇)
override itself does not cause dynamic dispatch

Dynamic dispatch happens because the method is non-final and overridable

Marking a method final forces static dispatch

class A {
    final func foo() {}
}

Protocol Dispatch (Common Pitfall)
Protocol Method Type	Dispatch
Protocol requirement	Dynamic (witness table)
Default implementation of requirement	Dynamic
Method only in protocol extension	Static
protocol P {
    func f()
}

extension P {
    func f() { print("default") }
}


✔️ If a conforming type implements f(), it will be called even via protocol type

One-Line Summary (Perfect for Notes)

Classes use dynamic dispatch for overridable methods; structs and enums always use static dispatch; protocol requirements are dynamically dispatched, but protocol extension-only methods are statically dispatched.


---

## 🧠 OOPs Concepts in Swift

In Swift, **Object-Oriented Programming (OOPs)** principles are implemented using classes, structures, and protocols. Below are the main OOPs concepts with explanations and Swift examples:

### 1. **Class and Object**
A **class** is a blueprint that defines properties and methods, and an **object** is an instance of a class.

```swift
class Car {
    var brand: String
    
    init(brand: String) {
        self.brand = brand
    }
    
    func drive() {
        print("\(brand) is driving the market")
    }
}

let tesla = Car(brand: "Tesla")
tesla.drive()
```


---

### 6. **Static and Dynamic Dispatch**

Swift uses **dynamic dispatch** for class methods marked with `override`. Structs and enums use **static dispatch**.

| Static Dispatch | Dynamic Dispatch |
|----------|--------|
| Happens at compile time — the compiler knows which method to call | Happens at runtime — the exact method to call is determined dynamically |
| Used for method overloading, functions in structs, enums, and final or static methods | Supports method overriding in classes (non-final). |
| No vtable (method lookup table) involved → faster execution | Maintains a vtable (virtual function table) to decide which overridden function to invoke |

ex - 
```swift
class Animal {
    func speak() { print("Animal sound") }
}

class Dog: Animal {
    override func speak() { print("Bark") }
}

let pet: Animal = Dog()
pet.speak() // Uses dynamic dispatch → "Bark"
```
Structs and enums don’t support inheritance → hence, no dynamic dispatch.

---

### 7. **Access Specifiers**

Swift provides five access control levels that determine where entities (like classes, structs, properties, and functions) can be accessed from:

| Level | Scope | Description | Typical Use |
|--------|--------|--------------|--------------|
| **open** | Anywhere (even outside the module) | Can be subclassed and overridden outside the module. | Framework APIs meant to be extended. |
| **public** | Anywhere (outside the module too) | Accessible anywhere but cannot be subclassed or overridden outside its defining module. | Public interfaces of frameworks. |
| **internal** (default) | Within the same module | Accessible anywhere within the app or framework. | Most internal app code. |
| **fileprivate** | Within the same file | Accessible only within the same Swift file. | Private helpers within a file. |
| **private** | Within the same scope | Accessible only inside the same type or its extensions within the same file. | Hiding implementation details tightly. |

Example:
```swift
public class Vehicle {
    private var speed = 0
    fileprivate func accelerate() { speed += 10 }
}
```

---

### 8. **Composition over Inheritance**
Swift encourages **composition** using protocols and extensions instead of deep inheritance.

```swift
protocol Flyable { func fly() }
protocol Swimmable { func swim() }

class Duck: Flyable, Swimmable {
    func fly() { print("Duck flying") }
    func swim() { print("Duck swimming") }
}
```

[⬆️ Back to Top](#top)
