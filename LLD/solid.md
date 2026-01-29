<a name="top"></a>

[<-- back](../LLD/lld.md)


# SOLID

1. SRP:
    - A class should have only one reason to change.
    - violations: if class handling multiple responsibilities ex - a UIViewController handling UI, api fetching and data validation logic

2. OCP:
    - Software entities should be open for extension but closed for modification. 
    - violations: if a class method have large if else blocks, or long switch cases statements

3. LSP:
    - Subclasses must be substitutable for their base classes without breaking expected behavior.
    - violations: when subclass changes the behavior of objects, ex a overridden subclass method fatalError which is not available in based class method

4. ISP:
    - Clients should not be forced to depend on methods they do not use.
    - violations: big fat protocols or classes implementing protocols method with empty function body.

5. DIP:
    - Depend on abstractions, not on concrete implementations.
    - violations: clases have implicit concrete initialization of objects



## SRP(Single Responsibility Principle):
**SRP states that a class should have only one reason to change, meaning it should handle only one responsibility, such as business logic or persistence, but not both.**

Ex - 1: in below example Journal is handling multiple responsibility, related to Journal, saving and loading to file
```
class Journal : CustomStringConvertible
{
  var entries = [String]()
  var count = 0

  func addEntry(_ text: String) -> Int
  {
    count += 1
    entries.append("\(count): \(text)")
    return count - 1
  }

  func removeEntry(_ index: Int)
  {
    entries.remove(at: index)
  }

  var description: String
  {
    return entries.joined(separator: "\n")
  }

  func save(_ filename: String, _ overwrite: Bool = false)
  {
    // save to a file
  }

  func load(_ filename: String) {}
  func load(_ uri: URI) {}
}

func main()
{
  let j = Journal()
  let _ = j.addEntry("I cried today")
  let bug = j.addEntry("I ate a bug")
  print(j)

  j.removeEntry(bug)
  print("===")
  print(j)
}
```

To fix srp violation here we should move the persistent logic to another class

```
class Persistence 
{
  func saveToFile(_ journal: Journal, 
    _ filename: String, _ overwrite: Bool = false)
  {
    
  }
}

//
let p = Persistence()
  let filename = "/mnt/c/sdjfhskdjhfg"
  p.saveToFile(j, filename, false)
//
```


Ex - 2
```
class ProfileViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()
        fetchUser()
        saveUserToDisk()
        validateEmail()
        setupUI()
    }

    func fetchUser() { /* network call */ }
    func saveUserToDisk() { /* persistence */ }
    func validateEmail() { /* business logic */ }
    func setupUI() { /* UI code */ }
}
```
Above violating SRP
Instead we need to move other logic other than rendering to other class

Ex - 3.  
MVVM:  
- View: UI rendering only
- ViewModel: Presentation + business logic
- Model: Data

VIPER:  
- View: 	UI
- Interactor: Business rules
- Presenter: Formatting
- Entity: Model
- Router: Navigation

## OCP(Open Closed Principle):
**Software entities should be open for extension, but closed for modification.**

Ex1: in ProductFilter below, if we want to add another filter we need to implement the func / modify the class which is a clear violation of OCP
```
enum Color
{
  case red
  case green
  case blue
}

enum Size
{
  case small
  case medium
  case large
  case yuge
}

class Product
{
  var name: String
  var color: Color
  var size: Size

  init(_ name: String, _ color: Color, _ size: Size)
  {
    self.name = name
    self.color = color
    self.size = size
  }
}

class ProductFilter
{
  func filterByColor(_ products: [Product], _ color: Color) -> [Product]
  {
    var result = [Product]()
    for p in products
    {
      if p.color == color 
      {
        result.append(p)
      }
    }
    return result
  }

  func filterBySize(_ products: [Product], _ size: Size) -> [Product]
  {
    var result = [Product]()
    for p in products
    {
      if p.size == size
      {
        result.append(p)
      }
    }
    return result
  }

  func filterBySizeAndColor(_ products: [Product], 
    _ size: Size, _ color: Color) -> [Product]
  {
    var result = [Product]()
    for p in products
    {
      if (p.size == size) && (p.color == color)
      {
        result.append(p)
      }
    }
    return result
  }
}

func main()
{
  let apple = Product("Apple", .green, .small)
  let tree = Product("Tree", .green, .large)
  let house = Product("House", .blue, .large)

  let products = [apple, tree, house]

  let pf = ProductFilter()
  print("Green products (old):")
  for p in pf.filterByColor(products, .green)
  {
    print(" - \(p.name) is green")
  }
}
```

Fix - 
```
protocol ProductSpecification {
  func isSatisfied(_ product: Product) -> Bool
}

class NewFilter {
  func filter(_ products: [Product], by spec: ProductSpecification) -> [Product] {
    var result = [Product]()
    for product in products {
      if spec.isSatisfied(product) {
        result.append(product)
      }
    }
    return result
  }
}

class ColorSpecification: ProductSpecification {
  let color: Color

  init(_ color: Color) {
    self.color = color
  }

  func isSatisfied(_ product: Product) -> Bool {
    return product.color == color
  }
}

class SizeSpecification: ProductSpecification {
  let size: Size

  init(_ size: Size) {
    self.size = size
  }

  func isSatisfied(_ product: Product) -> Bool {
    return product.size == size
  }
}

class AndSpecification: ProductSpecification {
  private let first: ProductSpecification
  private let second: ProductSpecification

  init(_ first: ProductSpecification, _ second: ProductSpecification) {
    self.first = first
    self.second = second
  }

  func isSatisfied(_ product: Product) -> Bool {
    return first.isSatisfied(product) && second.isSatisfied(product)
  }
}
```

// and use like below
```
print("Green products:")
let greenSpec = ColorSpecification(.green)
for p in filter.filter(products, by: greenSpec) {
  print(" - \(p.name)")
}

print("Large & green products:")
let largeSpec = SizeSpecification(.large)
let largeAndGreen = AndSpecification(largeSpec, greenSpec)
for p in filter.filter(products, by: largeAndGreen) {
  print(" - \(p.name)")
}
```

Ex 2- here every time a new type added, you need to modify it, instead of this you need a Login Strategy here
```
class LoginViewModel {

    func login(type: LoginType) {
        if type == .email {
            loginWithEmail()
        } else if type == .google {
            loginWithGoogle()
        }
    }
}
```

Q: **Does OCP mean never changing code**.  
A: No, It means we **should not modify existing tested code**.

## LSP(Liskov Substitution Principle):
**LSP states that objects of a superclass should be replaceable with objects of a subclass without breaking program correctness and behavior.**

- LSP is about behavior, not syntax
- If subclass changes expectations, LSP is broken
- Inheritance is the most common cause of LSP violations, but inheritance and overriding != LSP violation if behaviour is preserved
- Prefer protocols + composition in Swift

Ex1- below Square is rectangle, but here square modifies the behavior of rectangle, 
```
class Rectangle : CustomStringConvertible
{
  internal var _width: Int = 0
  internal var _height: Int = 0
  var width: Int
  {
    get {
      return _width
    }
    set(value) {
      _width = value
    }
  }
  var height: Int
  {
    get {
      return _height
    }
    set(value) {
      _height = value
    }
  }

  init(_ width: Int, _ height: Int)
  {
    self.width = width
    self.height = height
  }

  public var description: String
  {
    return "Width: \(width), height: \(height)"
  }
}

class Square : Rectangle
{
  var height: Int
  {
    get { return _height }
    set(value)
    {
      _width = value
      _height = value
    }
  }
  var width: Int
  {
    get { return _width }
    set(value)
    {
      _width = value
      _height = value
    }
  }
}

func area(_ r : Rectangle) -> Int {
  return r.width * r.height
}

func main()
{
  var rc = Rectangle(2,3)
  print("\(rc) has area \(area(rc))")

  // should be able to substitute a base type for a subtype
  var sq: Rectangle = Square(0,0)
  sq.width = 4
  print("\(sq) has area \(area(sq))")
}

main()
```

sol1 - instead we can use a flag(isSquare) but that violates SRP
Sol2 - using the Protocol Shape
```
protocol Shape {
    var area: Int { get }
}
final class Rectangle: Shape {
    let width: Int
    let height: Int

    var area: Int {
        width * height
    }

    init(width: Int, height: Int) {
        self.width = width
        self.height = height
    }
}
final class Square: Shape {
    let side: Int

    var area: Int {
        side * side
    }

    init(side: Int) {
        self.side = side
    }
}
```

Ex - 2
```
class NetworkService {
    func fetch() -> Data {
        return Data()
    }
}

class MockNetworkService: NetworkService {
    override func fetch() -> Data {
        fatalError("Not implemented")
    }
}
```
Breaks LSP — client expects valid Data

```
class MockNetworkService: NetworkService {
    override func fetch() -> Data {
        return Data("mock".utf8)
    }
}
```

So inheritance and override —> doesn’t always —> implies LSP breakage

## ISP(Interface Segregation Principle):
**ISP Clients should not be forced to depend on methods they do not use**

Ex - violating ISP, because every obj want to be machine have to implement all the methods
```
protocol Machine {
    func print(_ document: Document)
    func scan(_ document: Document)
    func fax(_ document: Document)
}

final class OldFashionedPrinter: Machine {
    func print(_ document: Document) {
        // print
    }

    func scan(_ document: Document) {
        fatalError("Not supported")
    }

    func fax(_ document: Document) {
        fatalError("Not supported")
    }
}
```

Solution: segregate into multiple smaller protocols
```
protocol Printer {
    func print(_ document: Document)
}

protocol Scanner {
    func scan(_ document: Document)
}

protocol Fax {
    func fax(_ document: Document)
}

Protocol MultiFunctionDevice: Printer, Scanner, Fax {
}

final class OrdinaryPrinter: Printer {
    func print(_ document: Document) {
        // print document
    }
}
```

Q: **ISP vs SRP** are same?  
A: **SRP applies to classes; ISP applies to interfaces** (protocols).


## DIP(Dependency Inversion Principle):
**High-level modules should not depend on low-level modules.**.  
Both should depend on abstractions.  
Abstractions should not depend on details.  
Details should depend on abstractions.


—> Business logic should not care about implementation details.

Ex1 - The problem with our is that high level module Research is dependent on the concrete class Relationships
**
enum Relationship
{
  case parent
  case child
  case sibling
}

class Person
{
  var name = ""
  // dob, etc
  init(_ name: String)
  {
    self.name = name
  }
}

class Relationships
{
  var relations = [(Person, Relationship, Person)]()

  func addParentAndChild(_ p: Person, _ c: Person)
  {
    relations.append((p, Relationship.parent, c))
    relations.append((c, Relationship.child, p))
  }

}

class Research
{
  init(_ relationships: Relationships)
  {
    // high-level: find all of job's children
    let relations = relationships.relations
    for r in relations where r.0.name == "John" && r.1 == Relationship.parent {
      print("John has a child called \(r.2.name)")
    }
  }
}
**
// Fix - 
**
protocol RelationshipBrowser
{
  func findAllChildrenOf(_ name: String) -> [Person]
}

extension Relationships: RelationshipBrowser {
  func findAllChildrenOf(_ name: String) -> [Person]
  {
    return relations.filter({$0.name == name && $1 == Relationship.parent && $2 != nil})
                    .map({$2})
  }
}

class Research
{
  …
  init(_ browser: RelationshipBrowser)
  {
    for p in browser.findAllChildrenOf("John")
    {
      print("John has a child called \(p.name)")
    }
  }
}
**

[⬆️ Back to Top](#top)
