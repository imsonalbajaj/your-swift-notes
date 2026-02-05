Swift and Type Safety

Swift is a type-safe language.
This means the compiler must know at compile time:

what type each variable holds

what types each function or method operates on

This allows Swift to catch errors early and guarantees safety.

Protocols and associatedtype

A protocol that declares an associatedtype is incomplete by itself.
It becomes complete only when a concrete type is chosen by the conforming type.

Meaning of associatedtype

associatedtype means:

“This protocol depends on a type, but the protocol itself does not decide what that type is.
The conforming type will decide it.”

Example
protocol PushInterface {
    associatedtype T
    func push(obj: T)
}


Here:

T is unknown inside the protocol

each conforming type must choose a concrete type for T

Concrete conforming types
class IntArr: PushInterface {
    func push(obj: Int) {}
}


T is inferred as Int

IntArr can only push Int values

class GenericArr<T>: PushInterface {
    func push(obj: T) {}
}


T remains generic

GenericArr<Int> pushes Int

GenericArr<String> pushes String

Why a protocol with associatedtype cannot be used directly
let p: PushInterface // ❌ not allowed


Reason:

Swift does not know what T is

type safety would be broken

some — opaque types

some means:

“This returns one specific concrete type, but the caller does not know which one.”

The compiler does know the concrete type.

Example
func makePusher() -> some PushInterface {
    IntArr()
}


Rules:

the function must always return the same concrete type

improves performance and keeps strong type safety

❌ Not allowed:

func makePusher() -> some PushInterface {
    if Bool.random() {
        return IntArr()
    }
    // return another type → error
}

any — existential types

any means:

“This can be any type that conforms to the protocol.”

Example
let p: any PushInterface // ❌ still not allowed


Even with any, this fails because:

associatedtype T is still unknown

Where any does work
protocol Animal {
    func makeSound()
}

let animals: [any Animal] = [Dog(), Cat()]


any is useful when:

you want to store different conforming types together

the protocol has no associated types

Type Erasure

Type erasure is used when:

a protocol has an associatedtype

you want to store or pass it around as a value

Type erasure hides the concrete type but keeps the required behavior.

Type-erased wrapper example
struct AnyPushInterface<T>: PushInterface {
    private let _push: (T) -> Void

    init<P: PushInterface>(_ pusher: P) where P.T == T {
        self._push = pusher.push
    }

    func push(obj: T) {
        _push(obj)
    }
}

Using type erasure
let intArr = IntArr()
let erased = AnyPushInterface(intArr)

erased.push(obj: 10)


Now:

the concrete type (IntArr) is hidden

the associated type (T) is known

Swift remains type-safe

Summary (memorize this part)

associatedtype → protocol depends on a type

some → one fixed concrete type, hidden from the caller

any → any conforming type (only works without associated types)

type erasure → hides concrete type while preserving behavior

One golden rule 🧠

If a protocol has an associatedtype,
you must use generics, some, or type erasure.