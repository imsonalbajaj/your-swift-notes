# Swift Basics — Complete Notes

## 1. Constants & Variables
- `let` → constant (A stored value which not going to change)
- `var` → variable (value can change)

```swift
let maxAttempts = 10
var currentAttempt = 0
```

- You can assign value during declaration, or later as long as the value is set before use.

- Multiple declarations in one line:
```swift
var x = 0.0, y = 0.0, z = 0.0
```

---

## 2. Type Annotations & Inference
- Type annotation: declaring what kind of values the constant or variable can store

```swift
var message: String
```

- Type inference determines type automatically:

```swift
let name = "Sonal"     // String
let score = 42         // Int
let pi = 3.14159       // Double
```

---

## 3. Naming Rules
- Unicode allowed: `π`, `你好`, `🐶🐮`
- Cannot start with numbers.
- Keywords can be wrapped in backticks:

```swift
let `class` = "value"
```

---

## 4. Printing & Interpolation
print(_:separator:terminator:) --> variadic function
```swift
print("Value is \(value)")
```

---

## 5. Comments
- Single-line: `//`
- Multi-line: `/* ... */`
- Nested comments allowed.

---

## 6. Semicolons
- Not required except multiple statements on one line:

```swift
let cat = "🐱"; print(cat)
```

---

## 7. Integers
- Whole numbers, exact math.
- Types like `Int8`, `UInt8`, `Int32`.
- Bounds:

```swift
UInt8.min  // 0
UInt8.max  // 255
```

### Int & UInt
- `Int` = platform-sized (32 or 64 bit).
- Prefer `Int` unless you specifically need unsigned.

---

## 8. Floating-Point Numbers
- `Float` (32-bit), `Double` (64-bit).
- Double is default inference.
- Floating-point values are approximate and can have:
  - `nan`, `inf`, `-inf`.

---

## 9. Type Safety
- Swift is a type safe language - meaning no implicit conversion occurs here while performing operations.
- No implicit type conversion(by either type annotation or type inference from the initialization)
- Explicit conversion required:

```swift
let value = Double(3) + 0.14
```
number literals --> Int
floating literals --> Double 

---

## 10. Numeric Literals
### Integer literals:
- Decimal: `17`
- Binary: `0b10001`
- Octal: `0o21`
- Hex: `0x11`

### Float literals:
- Decimal: `1.25e2`
- Hex float: `0xC.3p0`

### Readability:
```swift
let oneMillion = 1_000_000
```

---

## 11. Numeric Conversion
A number that won’t fit into a constant or variable prescribed type is reported as an error when your code is compiled
### Integer → Integer
Explicit:

```swift
UInt16(5)
```

### Integer ↔ Float
Explicit:

```swift
let pi = Double(3) + 0.14159
let intPi = Int(pi)   // truncates
```

---

## 12. Type Aliases

```swift
typealias AudioSample = UInt16
```

---

## 13. Booleans

```swift
let isValid = true
```

Swift requires explicit Bool in conditions.

---

## 14. Tuples
- Group multiple values:

```swift
let error = (404, "Not Found")
```

- Decompose:

```swift
let (code, message) = error
```

- Access via index: `error.0`
- Named values:

```swift
let http200 = (statusCode: 200, description: "OK")
```

---

## 15. Optionals
- Can represents absence of a value.

```swift
var x: Int? = nil
```

### Optional Binding

```swift
if let value = x { }
```

### Fallback (??)

```swift
let name = username ?? "Guest"
```

### Force unwrap (!)

---

## 16. Implicitly Unwrapped Optionals
- Use ONLY when value is guaranteed after initialization.

```swift
var text: String! = "Hello"
```

---

## 17. Memory Safety
Swift guarantees:
- No use-before-initialize
- Bounds checking
- No use-after-free
- Safe concurrent access rules

---

## 18. Error Handling

```swift
func canThrow() throws { }
```

Calling:

```swift
do {
    try canThrow()
} catch {
    print(error)
}
```

---

## 19. Assertions & Preconditions
### Assertion (debug only)

```swift
assert(age >= 0)
```

### Precondition (debug + release)

```swift
precondition(index > 0)
```

### fatalError

```swift
fatalError("Unimplemented")
```

---

# Advanced Questions and Answers

## Q1. Why doesn’t `let result = a + b` compile when `a = 5` and `b = 0.2`?
Swift is type-safe. You cannot add `Int` and `Double` without explicit conversion.

**Answer:**  
Integer literals can mix with floating-point literals, but variables cannot.  
You must explicitly convert:
```swift
Double(a) + b
```

---

## Q2. Explain differences between:
```swift
number.map { Double($0) }
Double(number!)
Double(number ?? 0)
```

**Answer:**
- `map` → returns `Double?`, safe, no crash.  
- `number!` → crashes if nil.  
- `number ?? 0` → returns non-optional `Double`.

---

## Q3. Why does this fail?
```swift
var a: Int
var b: Int

if Bool.random() { a = 10 } else { b = 20 }
print(a + b)
```

**Answer:**  
Swift enforces *definite initialization*. Each variable is initialized in only one branch, so Swift cannot guarantee both are set before use.

---

## Q4. Why does this compile?
```swift
let value = 1_000_000.0e-2
```
But this does not:
```swift
let a = 1_000_000
let b = a.e-2
```

**Answer:**  
The first is a literal parsed as one token.  
In the second, `a.e` is parsed as a property access, and `Int` has no member `e`.

---

## Q5. Explain optional shadowing:
```swift
let value: Int? = 42
if let value = value { print(value) }
print(value)
```

**Answer:**  
`if let` creates a new scoped variable that shadows the outer optional.  
Inside = unwrapped `Int`, outside = original `Int?`.

---

## Q6. Why does this crash?
```swift
let a: Int8 = 120
let b = a + 10
```
But this doesn’t:
```swift
let x: Int8 = 120
let y = x &+ 10
```

**Answer:**  
Normal `+` traps on overflow.  
`&+` performs two’s-complement wrap-around with no safety checks.

---

## Q7. Why does:
```swift
0.1 + 0.2 == 0.3   // false
```

**Answer:**  
Binary floating-point cannot represent 0.1/0.2/0.3 exactly.  
Each value becomes a different IEEE‑754 approximation.  
Compare with tolerance using `abs(x - y) < .ulpOfOne`.

---

## Q8. Optional chaining order:
```swift
user?.profile?.nickname?.count
```

**Answer:**  
Evaluation stops at first nil.  
Since `nickname` is nil, `.count` never runs → result is `nil`.

---

## Q9. Why is `guard` unwrapped value available after block but `if let` is not?

**Answer:**  
`if let` introduces a new inner-scope variable (shadowing).  
`guard let` promotes the unwrapped binding to the outer scope because the `else` must exit the function, guaranteeing initialization.

---

## Q10. Why does this compile?
```swift
let c = a + (b ?? 0)
```
But this does not:
```swift
let d = a + b
```

**Answer:**  
`b ?? 0` produces `Int`.  
`a + b` is `Int + Int?` → invalid; Swift never implicitly unwraps optionals.

---

## Q11. Why can’t Swift assign `nil` as a tuple element without specifying the tuple’s type?
**Answer:**  
Because `nil` alone has no type — it only means “absence of a value.” Swift requires full type information during initialization. A tuple containing `nil` must explicitly specify its type, otherwise the compiler cannot infer what type the tuple’s elements should be.

---

## Q12. Why does optional chaining stop evaluating after the first nil value?
**Answer:**  
Optional chaining evaluates expressions from left to right. As soon as any link in the chain is nil, the entire chain returns nil immediately. Swift stops evaluation to prevent accessing properties on non‑existent values, preserving type safety.

---

## Q13. Why does `if let value { }` shadow the outer optional but not change its value afterward?
**Answer:**  
Inside the `if let`, Swift creates a *new non‑optional constant* that shadows the outer optional. Inside the block, `value` refers to the unwrapped value. Outside the block, the original optional remains unchanged.

---

## Q14. Why does `let y: UInt8 = 300` not compile?
**Answer:**  
Swift checks integer literal bounds at compile time. Since `300` exceeds the range of `UInt8` (0–255), the assignment fails immediately. Swift never performs implicit wrapping; overflow only occurs when explicitly using `&+`, `&-`, or `&*`.

---

## Q15. Why doesn’t `let c = a + b` compile when both `a` and `b` are `Int?` created via `try?`?
**Answer:**  
`try?` converts thrown errors into optional values (`Int?`). Swift does not define `+` for optionals, and does not implicitly unwrap them. The expression `Int? + Int?` is invalid because addition requires concrete `Int` values. The optionals must be unwrapped or given fallback values.

---

## Q16. Why can’t you partially initialize a tuple by setting its elements one by one?
**Answer:**  
Swift requires *definite initialization*: a variable must be fully initialized before any part of it is accessed or mutated. Assigning `tuple.0` and `tuple.1` separately does not satisfy atomic initialization, so the tuple is considered uninitialized until the entire value is assigned at once.

---

## Q17. Why can’t Swift treat `1` as `true` in an `if` condition?
**Answer:**  
Swift enforces strict type safety. Integers cannot be implicitly converted to Boolean values. This prevents hidden logic bugs common in C-like languages. The condition must explicitly produce a `Bool`, such as `flag == 1`.

---

## Q18. Why does `(0.1 + 0.2) == Double(Float(0.3))` evaluate to false?
**Answer:**  
Swift infers floating‑point literals as `Double`. Values like 0.1, 0.2, and 0.3 cannot be represented exactly in binary floating‑point. The Double approximation of `0.1 + 0.2` differs from the Float approximation of `0.3` (later widened to Double). Due to IEEE‑754 rounding differences, strict equality fails; comparisons must use a tolerance.

---

## Q19. Why does using an implicitly unwrapped optional (`String!`) crash when it becomes nil?
**Answer:**  
`String!` auto‑unwraps when accessed, behaving like an implicit `value!`. If the IUO contains nil, the auto‑unwrap triggers a runtime crash. Swift assumes IUOs will never be nil at the moment of use, placing responsibility entirely on the developer.

---

## Q20. Why does `assert(input >= 0)` crash in debug mode even though the code compiles?
**Answer:**  
`assert` is a runtime check active only in debug builds. The compiler does not evaluate the condition at compile time, so it allows the code. At runtime, since the condition fails, the assertion triggers a debug‑mode crash. Unlike `assert`, `precondition` runs in both debug and release builds.



---

## Q21–Q30 — Advanced Questions and Answers (Appended)

### **Q21. Integer Conversion, Type Inference & Overflow Safety**
1. `a = 1000` is inferred as `Int` and compiles.
2. `let b: Int16 = a` fails because you cannot assign `Int` to `Int16` directly.
3. `Int16(a)` works only if the literal fits in range; otherwise fails.
4. `Int8(a)` fails due to compile‑time overflow.
5. Swift checks literal overflow at compile time.
6. Type safety prevents implicit narrowing.
7. Safe conversion requires range‑checking.

### **Q22. Typealias, Literal Overflow & Type Safety**
1. `typealias` does not create a new type.
2. `300` doesn't fit in `UInt8`.
3. Literals overflow at compile‑time.
4. Variables do not wrap implicitly.
5. Use clamping or larger integer types.
6. Typealias = cosmetic rename.
7. Struct wrapper creates new type.

### **Q23. Tuple Type Inference & Optional Rules**
1. `(10, nil)` → `(Int, Int?)`.
2. `nil` forces optional inference.
3. `Int` cannot convert to `Int?` implicitly.
4. Must match element types exactly.
5. Tuple inference is element‑wise.
6. Explicit type fixes mismatches.
7. Swift requires definite typing.

### **Q24. Optional Binding & Condition Order**
1. Optional binding stops at first nil.
2. Later conditions don't evaluate.
3. Bound values shadow outer ones.
4. No implicit unwrapping.
5. Conditions must be type‑safe.
6. Ensures memory safety.
7. Reorder checks to avoid skipping.

### **Q25. try? Behavior & Optional Propagation**
1. `try?` converts errors → `nil`.
2. Returns optional value.
3. Does not create `Result`.
4. `a ?? b` produces optional.
5. `try!` crashes; `do/catch` preserves error.
6. Optional math must unwrap.
7. Use `do/catch` to propagate.

### **Q26. Integer Literals, Underscores & Overflow**
1. `_` does not affect value.
2. `a` inferred as `Int`.
3. `UInt8` cannot store 10000.
4. `as Int8` overflows at compile time.
5. Literal overflow checked early.
6. Use larger types.
7. Clamping avoids overflow.

### **Q27. Optional Pattern Matching in Tuples**
1. `(name?, age?)` unwraps both.
2. `(name?, nil)` unwraps name only.
3. `?` in pattern auto‑unwraps.
4. Switch exhaustiveness checked.
5. Pattern resolves left → right.
6. Tuple labels irrelevant for matching.
7. Better than nested if‑lets.

### **Q28. Memory Safety & Inout Exclusivity**
1. Swift enforces exclusive access.
2. Different indices → allowed.
3. Overlapping read/write → error.
4. Compiler proves alias safety.
5. Dynamic indices may error.
6. Passing same element twice → illegal.
7. Ensures thread and memory safety.

### **Q29. Floating‑Point Precision & Equality**
1. 0.1/0.2/0.3 not exactly representable.
2. IEEE‑754 approximations differ.
3. Literal widening changes rounding.
4. Strict equality compares bits.
5. Use tolerance for comparison.
6. Doubles inferred by default.
7. Floats are approximate; ints are exact.

### **Q30. Preconditions, try?, and Runtime Behavior**
1. `precondition` executes before `try?`.
2. Failure terminates program.
3. Not catchable via `do/catch`.
4. `assert` debug‑only; `precondition` always checked.
5. `fatalError` always stops execution.
6. `try?` cannot convert crashes into nil.
7. Use `guard b != 0 else { return nil }` for safe division.


# Basic Operators — Notes

## 1. Operator Categories
- Unary → Operates on one value
    - Prefix: -a, !a
    - Postfix: a!
- Binary → Operates on two values
    - Infix: a + b, a == b
- Ternary
    - Only operator: a ? b : c

## 2. Assignment Operator
- Assigns right-hand value to left:  
    ``` swift
    var a = 5
    a = 10
    ```
- Does NOT return a value (unlike C).
    ```
    let (x, y) = (1, 2)
    ```
- Supports tuple decomposition.
    ```
    let (x, y) = (1, 2)
    ```

## 3. Arithmetic Operators
- `+`, `-`, `*`, `/`
- No silent overflow (throws runtime error).
- Overflow operators: `&+`, `&-`, `&*`
- Strings can be concatenated with `+`.

## 4. Remainder Operator
- Behaves as remainder, not pure modulo.
- Sign follows left operand(the right sign is going to be ignored).
    ```
    -9 % -4   // -1
    ```

## 5. Unary Operators
- Unary minus inverts sign.
    ```
    let x = 3
    let y = -x  // -3
    ```
- Unary plus returns value as-is.
    ```
    let a = +5 // just 5, included for symmetry
    ```

## 6. Compound Assignment
- `+=`, `-=`, etc.
- Do not return values.
    ```
    let b = a += 2 // compile error
    ```

## 7. Comparison Operators
- `==`, `!=`, `<`, `<=`, `>`, `>=`
- Return Bool.
- Tuple comparison
    - works left → right.
    - Only valid if each element supports the operator.  
    ```
        (1, "a") < (2, "b")     // true
        (3, "apple") < (3, "bird") // true
    ```

## 8. Ternary Operator
- `condition ? a : b`
- Shorthand for simple branching.

## 9. Nil-Coalescing Operator
- `a ?? b` unwraps optional or uses fallback.
- Short-circuits.  
    ```
    Optional<T> ?? T → returns T
    Optional<T> ?? Optional<T> → returns Optional
    ```

## 10. Range Operators
- Closed: `a...b`
- Half‑open: `a..<b`
- One‑sided: `a...`, `...b`, `..<b`

## 11. Logical Operators
left associative

- NOT: `!a`
- AND: `a && b` (short-circuits)
- OR: `a || b` (short-circuits)
- Use parentheses for clarity.


# Strings and Characters — Notes

## 1. Overview
- `String` is a collection of `Character` values.
- Fully Unicode‑compliant, fast, modern implementation.
- Value type with copy-on-write optimization.
- Bridged to `NSString` when importing Foundation.

## 2. String Literals
### Single‑line:
```swift
let s = "Hello"
```

### Multiline:
```swift
let multi = """
Line 1
Line 2
"""
```

Soft-wrapped (no newline added):
```swift
let soft = """
Hello \
World
"""
```

Indentation of closing `"""` defines how much leading whitespace is ignored.

## 3. Special Characters
- Escapes: `\0`, `\\`, `\t`, `\n`, `\r`, `\"`, `\'`
- Unicode scalars:
```swift
"\u{24}"     // $
"\u{2665}"   // ♥
"\u{1F496}"  // 💖
```

Multiline strings allow quotes without escaping.

## 4. Extended String Delimiters
Disable escapes:
```swift
#"Line1\nLine2"#
```

Enable escape inside:
```swift
#"Line1\#nLine2"#
```

Supports multiline with `#""" ... """#`.

## 5. Empty Strings
```swift
var s1 = ""
var s2 = String()
s1.isEmpty  // true
```

## 6. Mutability
```swift
var a = "Hi"
a += " there"     // OK

let b = "Hi"
b += "!"          // ❌ cannot mutate constant
```

## 7. Strings Are Value Types
- Assigning or passing creates a copy.
- Optimized with copy‑on‑write → actual copying only when necessary.

## 8. Working with Characters
Iterating:
```swift
for c in "Dog!🐶" { print(c) }
```

Character literal:
```swift
let mark: Character = "!"
```

String from characters:
```swift
String(["C","a","t","🐱"])
```

## 9. Concatenation
```swift
"hello" + " world"
var s = "hi"
s += "!"
```

Appending a Character:
```swift
s.append("!")
```

## 10. String Interpolation
```swift
let msg = "\(2 + 3) is 5"
```

With extended delimiters:
```swift
print(#"Value is \#(10)"#)
```

## 11. Unicode Concepts
### Unicode Scalars
21‑bit numbers representing characters.

### Extended Grapheme Clusters
A single `Character` = 1 or more Unicode scalars:
```swift
"\u{E9}"            // é
"\u{65}\u{301}"     // e + accent → also é
```

Composite examples:
```swift
"\u{1F1FA}\u{1F1F8}"  // 🇺🇸
```

## 12. Counting Characters
```swift
"Koala 🐨".count
```

Grapheme‑cluster aware:
```swift
var word = "cafe"
word.count          // 4
word += "\u{301}"
word.count          // still 4
```

## 13. String Indices
Strings cannot be integer-indexed.

```swift
let s = "Guten Tag!"
s[s.startIndex]                       
s[s.index(before: s.endIndex)]
s[s.index(after: s.startIndex)]
```

Out‑of‑range indexing → runtime error.

Iterating indices:
```swift
for i in s.indices { print(s[i]) }
```

## 14. Inserting & Removing
Insert:
```swift
welcome.insert("!", at: welcome.endIndex)
welcome.insert(contentsOf: " there",
               at: welcome.index(before: welcome.endIndex))
```

Remove:
```swift
welcome.remove(at: welcome.index(before: welcome.endIndex))
welcome.removeSubrange(range)
```

## 15. Substrings
Slices return `Substring`, not `String`.

Convert for long-term storage:
```swift
let sub = greeting[..<index]
let newString = String(sub)
```

Substrings share underlying storage with original string → efficient but not for long-term storage.

## 16. Comparing Strings
### Equality:
Canonical equivalence:
```swift
"\u{E9}" == "\u{65}\u{301}"   // true
```

Visually similar but different meaning:
```swift
"A" != "А"   // Latin vs Cyrillic
```

### Prefix / Suffix:
```swift
scene.hasPrefix("Act 1")
scene.hasSuffix("mansion")
```

## 17. Unicode Representations
### UTF‑8:
```swift
for u in s.utf8 { print(u) }
```

### UTF‑16:
```swift
for u in s.utf16 { print(u) }
```

### Unicode Scalars:
```swift
for scalar in s.unicodeScalars {
    print(scalar, scalar.value)
}
```

---


# Swift — Collection Types (Notes)

Swift provides **three primary collection types**:

- **Array** → Ordered collection, duplicates allowed  
- **Set** → Unordered, unique values only  
- **Dictionary** → Unordered key–value pairs, keys unique  

All collection types are **generic**, **type-safe**, and respect **value semantics**.


## Mutability of Collections

- `var` → collection is **mutable**
- `let` → collection is **immutable**
- Best practice: Make collections `let` unless mutation is required.


# Arrays

## What is an Array?
- Ordered collection of **same-typed** elements  
- Duplicates allowed  
- Backed by contiguous storage, optimized for random access

## Array Type Syntax
- Full form: `Array<Element>`
- Preferred shorthand: `[Element]`

Example:
```swift
var numbers: [Int] = [1, 2, 3]
```

## Creating Arrays

### Empty array
```swift
var items: [Int] = []
var items2 = [Int]()
```

### Array with default values
```swift
var threeDoubles = Array(repeating: 0.0, count: 3)
```

### Concatenating arrays
```swift
let six = a + b
```

### Array literal
```swift
var shoppingList = ["Eggs", "Milk"]
```

## Accessing & Modifying Arrays

### Count & empty check
```swift
array.count
array.isEmpty
```

### Append values
```swift
array.append("X")
array += ["Y", "Z"]
```

### Access by subscript
```swift
let first = array[0]
array[0] = "Updated"
```

⚠️ Out-of-range access → **runtime crash**

### Replace ranges
```swift
array[2...4] = ["A", "B"]
```

## Insert & Remove
```swift
array.insert("Maple", at: 0)
array.remove(at: 2)
array.removeLast()
```

## Iteration
```swift
for item in array { }
```

With index:
```swift
for (index, value) in array.enumerated() { }
```


# Sets

## What is a Set?
- Unordered collection  
- Unique elements  
- Must contain **Hashable** types  
- Very fast membership checks (`O(1)` average)

## Syntax
```swift
var set: Set<String> = []
var set2: Set = ["A", "B", "C"]
```

## Modifying Sets

Add:
```swift
set.insert("Jazz")
```

Remove:
```swift
set.remove("Rock")
set.removeAll()
```

Check membership:
```swift
set.contains("Hip hop")
```

## Iterating Sets
```swift
for item in set { }
```

Sorted:
```swift
for item in set.sorted() { }
```

## Set Operations
```swift
odd.union(even)
odd.intersection(even)
odd.subtracting(primes)
odd.symmetricDifference(primes)
```

## Membership Relations
```swift
a.isSubset(of: b)
a.isSuperset(of: b)
a.isDisjoint(with: c)
```


# Dictionaries

## What is a Dictionary?
- Unordered key–value collection  
- Keys must be **Hashable**
- Values can be any type  
- Updating a key replaces its previous value

## Syntax
- Full: `Dictionary<Key, Value>`
- Shorthand: `[Key: Value]`

Example:
```swift
var airports = ["YYZ": "Toronto Pearson", "DUB": "Dublin"]
```

## Creating Dictionaries

Empty:
```swift
var dict: [Int: String] = [:]
var dict2 = [Int: String]()
```

Literal:
```swift
var airports = ["YYZ": "Toronto", "DUB": "Dublin"]
```

## Accessing & Modifying

Add / update:
```swift
airports["LHR"] = "London Heathrow"
```

updateValue():
```swift
airports.updateValue("New Name", forKey: "YYZ")
```

Read:
```swift
let name = airports["DUB"]  // optional
```

Remove:
```swift
airports["APL"] = nil
airports.removeValue(forKey: "DUB")
```

## Iteration
```swift
for (code, name) in airports { }
```

Keys:
```swift
for key in airports.keys { }
```

Values:
```swift
for value in airports.values { }
```

Sorted:
```swift
for key in airports.keys.sorted() { }
```

# Key Takeaways

### Arrays
- Ordered, indexed, allow duplicates  
- Subscript access must be valid  

### Sets
- Unordered, unique elements  
- Great for fast lookups and set operations  

### Dictionaries
- Key–value storage  
- Keys must be unique & hashable  
- Value access returns optional


# Swift Control Flow — Notes

Swift provides powerful control-flow constructs for branching, looping, early exits, pattern matching, and deferred execution.

## 1. For-In Loops
- Iterate over arrays, dictionaries, ranges, strings, or any type conforming to `Sequence`.
- Dictionary iteration order is not guaranteed.
- Use `_` to ignore loop variables.
- Use `stride(from:to:by:)` or `stride(from:through:by:)` to skip values.

## 2. While Loops
- Use when number of iterations is unknown beforehand.
- Condition checked at start.
- Example shown with Snakes and Ladders logic.

## 3. Repeat-While Loops
- Executes body at least once.
- Condition checked at end.

## 4. Conditional Statements

### If
- Simple or chained with `else if` and `else`.
- Works for branching logic.
- `if` expression allows assignment with concise syntax, all branches must produce same type.

### If Expression (Swift 5.9+)
- Allows:
```
let value = if condition { a } else { b }
```
- Must return values of same type across branches.

## 5. Switch Statements
- Must be exhaustive.
- No implicit fallthrough.
- Can match:
  - Exact values
  - Intervals
  - Tuples
  - Value bindings
  - Patterns with `where`
  - Compound cases

### Interval Matching
- Supports ranges like `1..<5`.

### Tuple Matching
- Match combinations of multiple values.

### Value Binding
- Extract matched values using `let` or `var`.

### Where Clause
- Adds additional filtering.

### Compound Cases
- Combine multiple patterns:
```
case "a", "A":
```

## 6. Pattern Matching in `if` and Loops
- `if case` for matching patterns:
```
if case (let x, 100) = point { ... }
```

- `for-case-in` loops for iterating only matching patterns:
```
for case (let x, 0) in points { ... }
```

- Supports `where` for additional checks.

## 7. Control Transfer Statements

### continue
- Skip current iteration.

### break
- Terminate loop or switch statement.

### fallthrough
- Explicitly continues execution into next case.

## 8. Labeled Statements
- Control exactly which loop/switch is affected by break/continue.
```
outerLoop: while condition { ... }
```

## 9. Early Exit — guard
- Used for requirement checks.
- Must exit scope in else block.
- Unwrapped values remain available after guard.

## 10. defer
- Executes when leaving current scope.
- Runs in reverse (LIFO) order if multiple defers are present.
- Helpful for cleanup tasks.

## 11. Checking API Availability
- Ensures API is available on current platform.
```
if #available(iOS 10, macOS 10.12, *) { ... }
```
- `#unavailable` for inverse checks.
- With guard:
```
guard #available(iOS 10, *) else { return }
```
