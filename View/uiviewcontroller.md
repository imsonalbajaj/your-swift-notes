<a name="top"></a>

[<-- back](../README.md)

# iOS — UIViewController, UINavigationController & Storyboard

## 🧩 UIViewController

### What is UIViewController?
A **UIViewController** is a controller object that manages a screen’s content in an iOS app.  
It coordinates between the **view hierarchy** (UIView objects) and the **app’s data logic**.  
It also handles **system events**, **view lifecycle**, and **navigation** support.

> “A view controller manages a set of views that make up one screen of your app.”

### Why use UIViewController instead of just UIView?
- UIView only handles drawing and layout — it doesn’t manage events or transitions.
- UIViewController provides:
  - Lifecycle methods for screen appearance/disappearance.
  - Event handling (e.g., rotations, memory warnings).
  - Integration with navigation, tab bar, modal presentations.
  - Built-in support for state restoration and transitions.

### UIViewController Lifecycle
Below is the typical lifecycle flow:

| Method | When it’s called | Purpose |
|--------|------------------|----------|
| `init(coder:)` / `init(nibName:bundle:)` | When the VC is initialized | Setup non-view data |
| `loadView()` | When view property is accessed and is `nil` | Loads/creates the view hierarchy |
| `viewDidLoad()` | After the view is loaded into memory | Setup views, bindings, data sources |
| `viewWillAppear(_:)` | Before the view appears on screen | Update UI, register notifications |
| `viewDidAppear(_:)` | After the view appears | Start animations, API calls |
| `viewWillDisappear(_:)` | Before view disappears | Save state, stop animations |
| `viewDidDisappear(_:)` | After view disappears | Clean up observers, timers |
| `deinit` | When VC is released | Cleanup memory |

### Key Points:
- `loadView()` is called **only once** in the controller’s lifetime.
- If using Storyboard/XIB, iOS loads the view automatically.
- If not using Storyboard/XIB, `loadView()` must create and assign the view manually.

---

## 🧱 UIViewController Initialization

### 1️⃣ Storyboard-based Initialization
- The most common method.
- ViewController is defined in a Storyboard scene.
- Instantiated automatically or via:
  ```swift
  let vc = UIStoryboard(name: "Main", bundle: nil)
              .instantiateViewController(withIdentifier: "MyViewController")
  ```
- The view is loaded from the storyboard file.

### 2️⃣ XIB-based Initialization
- Each ViewController is associated with a `.xib` file.
- Initialized using:
  ```swift
  let vc = MyViewController(nibName: "MyViewController", bundle: nil)
  ```
- `loadView()` loads the view from the nib automatically.

### 3️⃣ Programmatic Initialization (No Storyboard/XIB)
- The view is created manually inside `loadView()` or `viewDidLoad()`.
  ```swift
  override func loadView() {
      view = UIView()
      view.backgroundColor = .white
  }
  ```
- Ideal for apps using UIKit with code-based UI or SwiftUI hosting.

---

## 🧭 UINavigationController

### What is UINavigationController?
A **container view controller** that manages a **stack of view controllers** for hierarchical navigation.

### Key Concepts:
- Follows a **stack-based navigation** (push/pop).
- Manages **navigation bar**, **back button**, and **transitions** automatically.
- Each child ViewController represents a **screen** in the navigation hierarchy.

### Common APIs:
```swift
navigationController?.pushViewController(detailVC, animated: true)
navigationController?.popViewController(animated: true)
navigationController?.popToRootViewController(animated: true)
```

### Lifecycle Considerations:
When using a navigation controller:
- `viewWillAppear` and `viewDidAppear` are triggered each time a screen is pushed or popped.
- Use these for dynamic updates (e.g., refreshing UI).

### UINavigationController Hierarchy:
```
UINavigationController
 ├── RootViewController
 ├── ChildViewController1
 └── ChildViewController2
```

---

## 🎬 Storyboard

### What is a Storyboard?
A **visual representation** of your app’s UI and navigation flow — managed by **Interface Builder**.

### Key Elements:
- Scenes → Represent individual ViewControllers.
- Segues → Define transitions between scenes.
- Storyboard ID → Used for programmatic instantiation.

### Types of Segues:
| Type | Description |
|------|--------------|
| Show (Push) | Pushes onto navigation stack |
| Modal (Present) | Presents a VC modally |
| Custom | User-defined transition |
| Unwind | Goes back to a previous VC |

### Instantiating a ViewController from Storyboard
```swift
let storyboard = UIStoryboard(name: "Main", bundle: nil)
let vc = storyboard.instantiateViewController(withIdentifier: "ProfileVC") as! ProfileViewController
present(vc, animated: true)
```

### Storyboard Advantages
- Visual and quick to design.
- Auto layout previews.
- Easy to manage segues and transitions.

### Storyboard Disadvantages
- Harder to merge in large teams.
- Less flexible for modular architectures (e.g., VIPER).
- Slower startup if Storyboard is large.

---

## 🧠 Summary

| Concept | Description |
|----------|-------------|
| **UIViewController** | Manages one screen’s UI and behavior |
| **UIView** | Handles drawing, layout, and touch within that screen |
| **UINavigationController** | Manages a stack of VCs for navigation |
| **Storyboard** | Visual design tool for building UI and flow |
| **XIB** | Lightweight, single-view design file |
| **Programmatic UI** | Created entirely via code |

---

## ⚡ Interview Tips

- **UIView vs UIViewController:** UIView handles layout; UIViewController manages behavior and lifecycle.
- **loadView():** Called automatically; override only if creating view manually.
- **Storyboard vs XIB:** Storyboard supports multiple scenes and segues; XIB is single-view.
- **NavigationController:** Enables push/pop-based navigation; always has one “root” VC.
- **Best Practice:** Keep UI setup in `viewDidLoad()` and dynamic updates in `viewWillAppear()`.

---

✅ **Recommended Reading:**
- [Apple Docs – UIViewController Class Reference](https://developer.apple.com/documentation/uikit/uiviewcontroller)
- [Apple Docs – UINavigationController](https://developer.apple.com/documentation/uikit/uinavigationcontroller)
- [Apple Docs – Storyboards and Segues Guide](https://developer.apple.com/documentation/uikit/uistoryboard)




[⬆️ Back to Top](#top)



