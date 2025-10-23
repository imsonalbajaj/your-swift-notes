<div align="right">

[<-- back](../Concurrency/concurrency.md)

</div>

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


## DispatchWorkItemFlags
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




## concurrentPerform 
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


<div align="right">

[<-- back](../Concurrency/concurrency.md)

</div>