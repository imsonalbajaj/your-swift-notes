# Swift Notes - UIView Layout & Appearance

### Layout → appearance 

- `clipsToBounds = true` - affects the view's subviews — anything that extends outside the view's bounds will be cut off.

### Geometry
- frame → position & size in superview’s coordinates.
- bounds → internal coordinate system (usually (0,0)).

### Initialization

- `init(frame:)` - gets called when you are making view programmatically
- `init(coder:)` - gets called when you are loading view from the nib or storyboard
- `awakeFromNib()` - gets called after the view had been loaded from the nib or storyboard

### View Hierarchy Lifecycle

- `willMove(toSuperview newSuperview: UIView?)` - Called before the view is added to (newSuperview → reference to new view) or removed from a UIWindow(newSuperview → nil)
- `didMoveToSuperview()` - Called after the view is added to or removed from a Superview
- `willMove(toWindow:)` - Called before the view is added to (toWindow → reference to new window) or removed from a UIWindow(toWindow → nil)
- `didMoveToWindow()` - Called after the view is added to (self.window → reference to new window) or removed from a UIWindow(self.window → nil)

### Layout Methods

- `layoutSubviews()` - Called whenever a view's bounds, constraint's or its subview's changes. You never call layoutSubviews() directly — you call setNeedsLayout() (asynchronous) or layoutIfNeeded() (synchronous) to trigger it. Its generally used to adjust the layout of view subviews. It affects geometry. Generally used to do manual frame-based layout
- `setNeedsLayout()` and `layoutIfNeeded()` - Used to trigger layoutSubviews, setNeedsLayout() runs in next run loop cycle (asynchronous), whereas layoutIfNeeded() runs immediately if current layout pass is pending(synchronous)

### Drawing Methods

- `draw()` - Used to perform the custom rendering, It's called when setNeedsDisplay() or setNeedsDisplay(_:) is invoked, or when the view first appears and needs to render. It affects pixels.
- `setNeedsDisplay()` - Calling setNeedsDisplay() tells the system to call draw(_:) during the next drawing cycle(asynchronously).

### CALayer

- Every uiview have the layer property, that helps it to render contents on screen, handle animation and various properties

### Touch Handling

- `hitTest()` - Used to determine which view should receive a touch event, UIKit recurcively calls the subviews and finds the most specific view that contains the touch point and (isUserInteractionEnabled = true and isHidden = false, alpha > 0.01 for that view)
- `point(inside:with:)`

### View Properties

- `isOpaque = true`
- `removeFromSuperview()`
- `isHidden = true`
- `isUserInteractionEnabled` - determines whether a view can receive touch events.

### Animation & Layout

- `UIView.animate`
- `sizeThatFits`

### Bounds & Clipping

- `view.layer.masksToBounds`
- `view.clipsToBounds`

### Coordinate System Conversion

- `convert(_ point: CGPoint, to view: UIView?)` → converts a point from the current view's coordinate system to another view's coordinate system.
- `convert(_ point: CGPoint, from view: UIView?)` → converts a point from another view's coordinate system to the current view's coordinate system.
- Similarly works for CGRect.



# iOS architecture

iOS follows a layered architecture, where each layer builds on the services of the one below it. 

Layer hierarchy:  Cocoa Touch(top level) → Media → Core Services → Core OS(bottom level)


## 1️⃣ Core OS Layer (Bottommost Layer)
This is the foundation of iOS where low-level components and system-level services reside.

Key Components:
- Kernel (XNU Hybrid Kernel): 
    Combines a mach microkernel(used to do scheduling, IPC and memory management) and the FreeBSD kernel(manages networking, file system and security services).
- Device Drivers: 
    Handle communication with hardware components such as the camera, microphone, and sensors.
- Security: Implements app sandboxing, manages trusted execution, encryption, and secure boot
- LibSystem: A C-based library providing fundamental system services like threading, networking, and file handling.
- File Management System(APFS)


## 2️⃣ Core Services Layer
Provides system-level services and APIs that apps rely on

Key Components:
- Core Foundation Services and Foundation Framework: Low-level data management, preferences, and utility APIs.
- Databases: Includes Core Data and SqLite support.
- Networking Services: Uses CFNetwork high level networking api.
- File System Access: Provides structured file handling APIs.
- Concurrency: GCD (Grand Central Dispatch) and NSOperationQueue built on this layer.
- Core Location:
    Access geolocation data
- AddressBook:
    Access contacts and events
- Core Motion: Access accelerometer and gyroscope data
- CloudKit: Sync data using icloud


## 3️⃣ Media Layer
Responsible for graphics, animation, audio, and video—everything that creates the visual and rich multimedia experience.


Key Components:
- Graphics:
	*	Core Graphics (Quartz 2D): 2D drawing and rendering. - used to draw line, quadrilaterals etc
	*	Metal: High-performance graphics and GPU-based rendering (used in games and 3D apps).
	*	Core Image: Used to alter the image

- Animation:
	*	Core Animation: Used to animate UI elements efficiently across multiple layers.


- Audio & Audio:
	*	Core Audio, AVFoundation for Playback, recording, and processing of audio.
	*	AVFoundation for Video capture, playback, and editing.

- Core Text:
Advanced text layout and font rendering.
- Core Video:
    For frame-by-frame video processing.
- SpriteKit / SceneKit:
For 2D/3D game rendering and animations.


## 4️⃣ Cocoa Touch Layer (Topmost Layer)
The application layer, where developers interact with iOS APIs to build apps

Key Components:

-	UI Frameworks:
    *	UIKit: imperative UI framework.
    *	SwiftUI: declarative UI framework.


-	Event Handling: Manages touch, gestures, and other user interactions.


-	View Controllers: Manage screen-level UI logic and the app’s view hierarchy.
-	Auto Layout: Handles responsive and adaptive layout for different screen sizes.
-	Other Services: Notifications, multitasking, and accessibility APIs.

- MapKit: Embeds interactive maps and annotations.
- MessageUI: Send emails or SMS from within apps.
- HealthKit / HomeKit: Access health data or control Home automation devices.
- UserNotifications: Handle local and push notifications.
- App Lifecycle Management:
Includes UIApplication, AppDelegate, and SceneDelegate for app startup, state transitions, and termination.
- Multitasking & Background Execution:
APIs for background fetch, audio playback, and location updates.



NOTE:
```
Kernel / FreeBSD Sockets 
   ↓
LibSystem (C Networking APIs)
   ↓
CFNetwork (Foundation-level networking)
   ↓
URL Loading System (NSURLSession, Swift APIs)
```

FLOW Diagram:
```

📱 User Interaction
        ↓
👆 Cocoa Touch Layer
    ├─ UIKit / SwiftUI → Build UI, handle events, manage view controllers
    ├─ UserNotifications / MapKit / HealthKit
    └─ Interacts with system APIs via Core Services

        ↓
🎛️ Media Layer
    ├─ Core Graphics / Core Animation → Draw and animate visuals
    ├─ Metal / Core Image → GPU-based rendering and image processing
    └─ AVFoundation / Core Audio → Play, record, and process media

        ↓
⚙️ Core Services Layer
    ├─ Core Data / SQLite → Data persistence
    ├─ CFNetwork / Foundation → Networking and utilities
    ├─ Core Location / Core Motion → Sensors & GPS
    ├─ GCD / OperationQueue → Concurrency
    └─ Provides APIs used by upper layers

        ↓
🧩 Core OS Layer
    ├─ XNU Hybrid Kernel (Mach + FreeBSD)
    ├─ Device Drivers → Camera, sensors, display
    ├─ Security Framework → Sandboxing, encryption
    ├─ LibSystem → C-based low-level APIs (threading, sockets)
    └─ APFS File System, Power Management, Bluetooth

        ↓
💾 Hardware
    (CPU, GPU, Memory, Sensors, Battery, Network Interfaces)

```

# Concurrency

#### Parallel vs Concurrent vs Serial
- Serial: Executes one task at a time, completing each before starting the next
- Concurrent is when you share computing resources between multiple blocks using context switching so that each gets fair chance for their execution
- parallelism is when you have multiple cores so that your tasks can actually run simultaneously

## GCD

#### Dispatch Queues

These are queues where you can submit your tasks. GCD (Apple’s concurrency framework) is responsible for executing them efficiently on system-managed threads.

- Serial Queue – Executes one task at a time, in FIFO order. Best for data consistency and thread safety.

- Concurrent Queue – Starts multiple tasks in order but executes them in parallel if system resources allow.


#### Types of Queues:

- Main Queue – A special serial queue for all UI updates. Guarantees that all tasks execute on the main thread.

- Global Queues – Predefined concurrent queues with predefined QoS levels:  
.userInteractive, .userInitiated, .default, .utility, .background

- Custom Queues - User-defined queues that internally use system threads.
    * let serial = DispatchQueue(label: "com.example.serial")
    * let concurrent = DispatchQueue(label: "com.example.concurrent", attributes: .concurrent)


#### Task Execution

- Synchronous (blocking):  
    It waits for the submit block to get finished before continuing further.  
    queue.sync{ }

- Asynchronous (non-blocking):  
    It’s a non-blocking call — it schedules the block and continues executing the current task.
    queue.async { }

#### Dispatch Groups
Used to wait for multiple asynchronous tasks to complete before proceeding.  
```
let group = DispatchGroup()
queue.async(group: group) { ... }
queue.async(group: group) { ... }
DispatchQueue.main.async(group: group) {
    ...
}
group.notify(queue: .main) {
    // notify is called once all tasks in the group finish, and it always runs asynchronously on the specified queue
}
```


#### Dispatch Semaphore.  
Used to control access to a resource or limit concurrency.  
Commonly used to serialize access to critical sections

```
let semaphore = DispatchSemaphore(value: 2)
semaphore.wait()   // Decrements count or waits if 0
// Critical section
semaphore.signal() // Increments count
```

#### DispatchWorkItem.  
A wrapper object for a block of work you can submit, cancel, or notify.  
```
let workItem = DispatchWorkItem {
    print("Work item executed")
}
queue.async(execute: workItem)
workItem.cancel()  // Cancels if not yet started
```


#### Dispatch Precondition.  
Used to assert that code is running on a specific queue (for debugging and thread safety).
```
dispatchPrecondition(condition: .onQueue(DispatchQueue.main))
```

#### asyncAfter
Schedules a task to be added to the queue after a given delay (does not block the thread). ex - 
```
DispatchQueue.main.asyncAfter(deadline: .now() + 3.0) {
    print("Executed after 3 seconds")
}
```
Internally, GCD sets a timer and enqueues the block once the deadline expires.  It delays the enqueueing of the task, not the execution of the queue itself.


#### Deadlock.  
A situation where two tasks are waiting on each other, causing both to hang indefinitely.
ex - 
```
DispatchQueue.main.sync {
    // Called from main thread → DEADLOCK
}
```
curr --> waiting for block to gets finished.  
block --> waiting for curr execution to gets finished

#### Suspend and resume Queues.  
- queue.suspend(): Temporarily pauses the queue — new tasks won’t start executing. Already running tasks will finish, but pending ones are held back.  
- queue.resume(): it resumes the previously suspended tasks.  
```
queue.suspend()
// ... some operations ...
queue.resume()
```
``if you suspend a queue and forget to resume it, queued tasks will never execute.``


#### QoS (Quality of Service)
Defines the priority of a task:
- userInteractive - Ul updates (highest priority)
- userInitiated - Immediate results for user-initiated actions
- utility - Time-consuming tasks (downloads, parsing)
- background - Maintenance, syncing (lowest)


#### Dispatch Sources

Dispatch Sources are low-level GCD objects used to monitor system events and automatically execute handlers when those events occur.
```
let source = DispatchSource.makeTimerSource(queue: .main)
source.schedule(deadline: .now(), repeating: 2)
source.setEventHandler {
    print("Timer fired!")
}
source.resume()
```

#### Thread safety
Handling inconsistency when shared resources are accessed by multiple threads simultaneously
- Using serial queues for shared resource access
- using `barrier` for exclusive writes on concurrent queues
- Using DispatchSemaphore to limit concurrent access
- main queue
- Avoid mutable shared state without synchronization


### DispatchWorkItemFlags
#### 1. `barrier`: 
Ensures exclusive execution in a concurrent queue.
All previously submitted tasks must finish before the barrier runs, and no new tasks start until it completes.  
Works only on custom concurrent queues, not on global queues (`barriers are ignored on global queues`).
```
queue.async(flags: .barrier) {
    // Exclusive write or critical section
}
```
Useful for implementing thread-safe read/write patterns

#### 2. detached:
.detached ensures the work item does not inherit QoS, context, or execution attributes from its creator.  
Even though GCD doesn’t provide structured concurrency, it can still propagate QoS or context from the queue that creates the work item.  
Using .detached explicitly prevents that inheritance — it ensures full isolation of execution context.
Ex- 
```
// Without detached
let highPriorityQueue = DispatchQueue.global(qos: .userInteractive)

highPriorityQueue.async {
    // Inherits high QoS automatically
    DispatchQueue.global().async {
        print("Still running as high priority")
    }
}
```
```
// With detached
let highPriorityQueue = DispatchQueue.global(qos: .userInteractive)

highPriorityQueue.async {
    // Runs independently, not inheriting parent QoS
    let item = DispatchWorkItem(flags: .detached) {
        print("Runs detached, independent of parent QoS")
    }
    DispatchQueue.global(qos: .background).async(execute: item)
}
```


#### 3. assignCurrentContext:
Explicitly inherits the current queue’s QoS and execution context when dispatching to a different queue.  
Normally, plain closures automatically escalate QoS if running inside a higher-priority context.
But DispatchWorkItem does not inherit automatically — you need .assignCurrentContext to enforce it.  

a. Implicit Escalation (plain closure)
```
DispatchQueue.global(qos: .utility).async {
// We're running in .utility context

    DispatchQueue.global(qos: .background).async {
        // This block may inherit utility QoS automatically
        // (QoS escalation)
    }
}
```
If the current QoS is higher, GCD performs QoS escalation — meaning, it runs the inner task at least at .utility to avoid priority inversion.


b. Explicit DispatchWorkItem
```
let highQoS = DispatchQueue.global(qos: .userInitiated)
let lowQoS = DispatchQueue.global(qos: .background)

highQoS.async {
    let workItem = DispatchWorkItem {
        print("QoS:", qos_class_self())  // May still be background
    }
    lowQoS.async(execute: workItem)
}
```
This runs at the target queue’s QoS (.background), because a DispatchWorkItem doesn’t automatically inherit from its creator.

With .assignCurrentContext:
```
highQoS.async {
    let workItem = DispatchWorkItem(flags: .assignCurrentContext) {
        print("QoS:", qos_class_self())  // userInitiated
    }
    lowQoS.async(execute: workItem)
}
```


Qos inheritance rules:
| Case | Ex | QoS Behavior | Notes |
| :------- | :-----: | --------: | --------: |
| Plain closure (async {}) | Nested async from high to low QoS | Inner block inherits or escalates to higher QoS | Automatic escalation between closures |
| Work item (default) | Submitted to a lower QoS queue | Runs at target queue’s QoS | No automatic inheritance |
| Work item + .assignCurrentContext | Created in high QoS, submitted to lower one | Retains parent’s QoS/context | Explicit inheritance |
| Work item + .detached | Any queue | Ignores parent QoS/context | Full isolation |

i.e.
• Plain closures → implicit inheritance/escalation.  
• DispatchWorkItem → no inheritance unless you use .assignCurrentContext.  
• .detached → explicit isolation (no inheritance at all).




### concurrentPerform. 
- DispatchQueue.concurrentPerform(iterations:execute:) runs a loop concurrently — it executes the given block multiple times in parallel, distributing the work across multiple threads managed by GCD.
- It’s synchronous, meaning the function waits until all iterations finish before returning.
- Internally, GCD optimizes how many threads to use based on available CPU cores, QoS, and system load.
- Tasks inside concurrentPerform are submitted to a global concurrent queue, not the queue from which it’s called.
- It is generally used for data-parallel or compute-heavy operations that can run independently.  

ex1-
```
DispatchQueue.concurrentPerform(iterations: 5) { index in
    print("Task \(index)")
}
```  

ex2- main thread blocks here, but not a deadlock
```
DispatchQueue.main.async {
    print("on main")

    DispatchQueue.concurrentPerform(iterations: 5) { index in
        print("Task \(index)")
    }
}
```

ex3- deadlock
```
// Runs synchronously on the main thread
DispatchQueue.concurrentPerform(iterations: 5) { index in
    // ❌ If you touch the main thread here → deadlock or freeze
    DispatchQueue.main.sync {
        print("Iteration \(index)")
    }
}
```

ex4- 
```
let queue = DispatchQueue(label: "com.example.concurrent", attributes: .concurrent)

queue.async {
    // concurrentPerform runs synchronously on this same queue
    DispatchQueue.concurrentPerform(iterations: 3) { index in
        // ❌ Deadlock condition:
        // Each iteration tries to run sync on the SAME queue it’s already running on
        queue.sync {
            print("Task \(index)")
        }
    }
}
```

concurrentPerform is synchronous — it blocks the thread from which it’s called until all iterations complete. Avoid calling it directly on the main thread for heavy work to prevent UI freeze.