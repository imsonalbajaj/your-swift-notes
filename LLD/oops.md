<a name="top"></a>

[<-- back](../LLD/lld.md)



# OOP's

1. Abstraction
    - Expose only what’s necessary, hide implementation details(using protocols).

2. Inheritance(is-a relation)
    - One class acquires properties & methods of another class.
    - limitation(tight - coupling) --> sol'n - composition

3. Encapsulation
    - Bundling data + methods together and controlling access using access specifiers.

4. Polymorphism
    - Same interface, different behavior


## Abstraction:
Abstraction hides complex implementation details and exposes a simple interface. Swift achieves this using **protocols**.

```swift
protocol Vehicle {
  var name: String { get set }
  func start()
}

// MARK: - Concrete implementation
class Car: Vehicle {
  var name: String

  init(name: String) {
    self.name = name
  }

  func start() {
    turnBatteryOn()
    startStarterMotor()
    pumpFuel()
  }

  func turnBatteryOn() { /../ }

  func startStarterMotor() { /../ }

  func pumpFuel() { /../ }
}

// MARK: - Concrete implementation
class Boat: Vehicle {
  var name: String

  init(name: String) {
    self.name = name
  }

  func start() {
    turnBatteryOn()
    pullStarterRope()
    pumpFuel()
  }

  func turnBatteryOn() { /../ }

  func pullStarterRope() { /../ }

  func pumpFuel() { /../ }
}

```

## Inheritance:
Inheritance allows a class to derive from another class, reusing and extending its functionality(Only class supports inheritance).  
Swift supports single inheritance, meaning a class can inherit from only one superclass directly. This solve multiple inheritance diamond problem

ex - 
```
class Vehicle {
    var speed: Double = 0.0
    var fuelLevel: Double = 0.0

    func fillTank() {
        fuelLevel += 10
    }

    func throttle() {
        if fuelLevel > 0 {
            speed += 10
            fuelLevel -= 5
        } else {
            print("Out of fuel!")
        }
    }
}

class Car: Vehicle {
}

class Motorcycle: Vehicle {
}

```

**limitation**:
Inheritance decrease the redundancy of code, but this concept leads to a **tight coupling** in your code, making it less flexible. Ex - if we need to introduce electric or hybrid cars, we can't change without modifying Vehicle class.  
soln - composition


## Encapsulation:
Encapsulation is the concept of data hiding and exposes only what’s necessary using access control keywords like `fileprivate`, `private`, `internal`, `public`, 'open'.
Hiding data (internal state) from outside access.

```swift
class Account {
    private var balance: Double = 0.0
    
    func deposit(amount: Double) {
        balance += amount
    }
    
    func getBalance() -> Double {
        return balance
    }
}
```

## Polymorphism:
Polymorphism is the multiple interpretation of same name method.

a. **Compile-time (Overloading)**  
same function name, different parameters
```swift
class Math {
    func add(a: Int, b: Int) -> Int { a + b }
    func add(a: Double, b: Double) -> Double { a + b }
}
```

b. **Runtime (Overriding)**  
subclass provides a specific implementation of a superclass method
use override 
```swift
class Animal {
    func sound() { print("Some sound") }
}

class Dog: Animal {
    override func sound() { print("Bark") }
}

let dog: Animal = Dog()
dog.sound()
```

[⬆️ Back to Top](#top)