<a name="top"></a>

[<-- back](../Content.md)



---

## . **class vs static**
    | Aspect | Static | class |
    | ------- |----------|-------------------|
    | Usage | Used in classes, structs, enums | Used only in classes |
    | Overriding | ❌ Cannot be overridden in subclass | ✅ Can be overridden using override class |
    | Dispatch type | Static dispatch (compile-time binding) | Dynamic dispatch (runtime binding via vtable) |
    | Belongs to | Type itself (not instances) | Type itself (but supports inheritance for classes) |
    | Common use | Utility constants or helper functions | Methods meant to be shared but still overridable |

---

## . **strored vs computerd vs lazy stored properties**
    | Aspect | Static | class |
    | ------- |----------|-------------------|
    **DO**

## Important Swift Keywords


### Concurrency
- `async` / `await`: Used for asynchronous operations.
- `actor`: Provides thread-safe isolation.
- `Task`: Represents a unit of async work.
- `MainActor`: Ensures execution on the main thread.

### Control Flow
- `guard`: Early exit when condition fails.
- `defer`: Runs code before scope exits.
- `break`, `continue`, `fallthrough`: Loop and switch control flow.

### Classes & Structs
- `final`: Prevents subclassing.
- `static`: Type-level property or method.
- `mutating`: Allows a struct or enum method to modify `self`.
- `required`: Forces subclasses to implement an initializer.
- `convenience`: Secondary initializer.
- `override`: Overrides superclass implementation.

### Closures
- `in`: Separates closure parameters from body.
- `@escaping`: Closure stored or executed later.
- `@autoclosure`: Automatically wraps an expression in a closure.

### Protocols & Extensions
- `protocol`: Defines required behavior.
- `extension`: Adds functionality to existing types.
- `associatedtype`: Placeholder type in protocol.
- `where`: Adds constraints in generics or protocols.

### Error Handling
- `throws`, `try`, `catch`, `rethrows`: Define and handle errors.
- `do { ... } catch { ... }`: Error-handling block.

### Miscellaneous
- `typealias`: Type alias.
- `subscript`: Indexed access.
- `optional` (`?`, `!`): Value that may be nil.
- `self`: Current instance.
- `super`: Superclass access.





[⬆️ Back to Top](#top)