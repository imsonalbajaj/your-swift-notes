
Content
- [Keywords](/Others/keywords.md)
- [oops](/Others/oops.md)
- [UIView](/View/uivew.md). 
- [UIViewController](/View/uiviewcontroller.md). 
- [ios-OS-architecture](/Others/iosOSarchi.md)
- [Concurrency](Concurrency/concurrency.md)
- [Deeplink](Others/deeplink.md)
- [LLD](LLD/lld.md)
    1. [OOP's](LLD/oops.md).
        1. [Abstraction](LLD/oops.md#abstraction)
            - Expose only what’s necessary, hide implementation details(using protocols).
        
        2. [Inheritance(is-a relation)](LLD/oops.md#inheritance)
            - One class acquires properties & methods of another class.
            - limitation(tight - coupling) --> sol'n - composition

        3. [Encapsulation](LLD/oops.md#encapsulation)
            - Bundling data + methods together and controlling access using access specifiers.

        4. [Polymorphism](LLD/oops.md#polymorphism)
            - Same interface, different behavior  

    2. [Design Principles(SOLID)](LLD/solid.md).
        1. [SRP:](LLD/solid.md#srpsingle-responsibility-principle).
            - A class should have only one reason to change.
            - violations: if class handling multiple responsibilities ex - a UIViewController handling UI, api fetching and data validation logic

        2. [OCP:](LLD/solid.md#ocpopen-closed-principle).
            - Software entities should be open for extension but closed for modification. 
            - violations: if a class method have large if else blocks, or long switch cases statements

        3. [LSP:](LLD/solid.md#lspliskov-substitution-principle).
            - Subclasses must be substitutable for their base classes without breaking expected behavior.
            - violations: when subclass changes the behavior of objects, ex a overridden subclass method fatalError which is not available in based class method

        4. [ISP:](LLD/solid.md#ispinterface-segregation-principle).
            - Clients should not be forced to depend on methods they do not use.
            - violations: big fat protocols or classes implementing protocols method with empty function body.

        5. [DIP:](LLD/solid.md#dipdependency-inversion-principle).
            - Depend on abstractions, not on concrete implementations.
            - violations: clases have implicit concrete initialization of objects
    3. Design Patterns
    4. Architecture Designs(MVC/MVVM/VIPER)