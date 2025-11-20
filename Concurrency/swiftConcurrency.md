
Swift concurrency -> run on same system thread --> some task might run on a thread then suspended and then can be resumed on some other thread

# Detailed Notes - Swift Concurrency Overview

**What is Concurrency?**  
Concurrency allows multiple tasks to make progress at the same time, even on a single thread.  
It’s about structuring programs so they can perform work asynchronously without blocking threads.

- **Asynchronous code** can be suspended and resumed later.
- **Parallel code** runs multiple pieces of code simultaneously on different CPU cores.
- Together, they form what we refer to as **concurrency** in Swift.

---

**Threads vs Tasks**
- Threads are heavyweight OS-level constructs.  
- Tasks are lightweight units of asynchronous work managed by the Swift runtime.  
- Many tasks can run cooperatively on a few system threads.  

Swift Concurrency uses a *cooperative thread pool* managed by the Swift runtime.  
Tasks suspend instead of blocking threads, so threads are reused efficiently.

---

**Suspending Instead of Blocking**
When an async function hits an `await`, execution is **suspended** —  
the thread is freed for other work. When the awaited task completes, Swift resumes it, possibly on a different thread.

```swift
func fetchImage() async -> UIImage {
    let (data, _) = try! await URLSession.shared.data(from: url)
    return UIImage(data: data)!
}
```

---

**Structured Concurrency**
- Ensures child tasks are automatically cancelled when the parent is cancelled.
- Prevents leaks and unbounded background work.
- Makes async code predictable and memory-safe.

Example:
```swift
await withTaskGroup(of: Void.self) { group in
    group.addTask { await loadUserData() }
    group.addTask { await loadProfileImage() }
}
```

---

**Analogy**
> Think of a restaurant: each waiter (thread) serves multiple tables (tasks).  
> When one table is waiting (await), the waiter serves another table.  
> This keeps all resources efficiently utilized.

---

**Common Interview Takeaways**
- Concurrency ≠ Parallelism — concurrency is about progress; parallelism is about simultaneous execution.
- `await` suspends, doesn’t block.
- The Swift runtime manages scheduling through a cooperative thread pool.
- Tasks are safer, lighter, and structured compared to GCD threads.
- Async functions must either `await` or use `async let` for concurrent execution.

---

---

# 🧠 Detailed Notes — Defining and Calling Asynchronous Functions

**Synchronous vs Asynchronous Functions**

| Type | Behavior |
|------|-----------|
| **Synchronous** | Runs to completion, throws an error, or never returns. |
| **Asynchronous (`async`)** | Can pause execution mid-way and **resume later** when awaited work completes. |

Async functions improve responsiveness by not blocking threads.  
You mark them with the `async` keyword after parameters, similar to how you use `throws`.

```swift
func listPhotos(inGallery name: String) async -> [String] {
    let result = ["IMG_001", "IMG_002"]
    return result
}
```

If both `async` and `throws` are used:
```swift
func fetchData() async throws -> Data
```
You write `async` before `throws`.

---

**Calling Asynchronous Functions**
- You call async functions using `await`, which marks a suspension point.  
- Suspension only happens at explicit `await` calls — never implicitly.

```swift
let photoNames = await listPhotos(inGallery: "Summer Trip")
let firstPhoto = await downloadPhoto(named: photoNames[0])
show(firstPhoto)
```

Each `await` line pauses execution and allows other code to run.

---

**Execution Flow**
1. Execution runs until the first `await`.
2. The function suspends and yields the thread.
3. Once the awaited operation completes, Swift resumes the function (possibly on a different thread).
4. Code continues sequentially from the suspension point.

---

**Suspension Points and Thread Hopping**
- Suspension is cooperative — Swift decides when to suspend/resume.
- When resuming, execution might continue on a different thread.
- Use `@MainActor` for UI updates to ensure main-thread execution.

---

**Rules for Calling Async Functions**
Async functions can only be called from:
1. Another async function or property.
2. The `@main` async entry point.
3. A `Task {}` or `DetachedTask {}` closure.

```swift
Task {
    await showGallery()
}
```

---

**Async + Throwing Functions**
Combine `try` and `await` when calling throwing async functions:

```swift
let photos = try await listPhotos(inGallery: "Rainy Weekend")
```

Here, `try` marks a possible error and `await` marks a possible suspension.

---

**No Async-to-Sync Bridge**
- You cannot call async functions directly from synchronous code.  
- Swift intentionally omits blocking wait APIs to prevent deadlocks and thread starvation.  
- Always convert top-down: make parent functions async first.

---

**Task.sleep(for:)**
Use to simulate suspension or delays:
```swift
try await Task.sleep(for: .seconds(2))
```
This suspends without blocking the thread (unlike `Thread.sleep()`).

---

**Analogy**
> Think of `await` as pressing pause on a movie; the projector (thread) is free to play another one until your turn resumes.

---

### ❓ Interview-style Questions and Answers

**Q1. What does the `async` keyword indicate in a function declaration?**  
It marks a function as asynchronous — capable of suspending and resuming later without blocking the thread.

---

**Q2. What happens at each `await` point in async code?**  
The function suspends and yields its thread to the runtime.  
When the awaited operation finishes, it resumes — possibly on another thread.

---

**Q3. Can you call async functions from synchronous code directly?**  
No. Wrap it in a `Task {}` or convert the caller to async.  
Swift forbids synchronous blocking waits.

---

**Q4. Why is there no built-in way to “wait synchronously” for async results?**  
Because it would block threads, cause deadlocks, and break Swift’s cooperative concurrency model.

---

**Q5. What’s the correct order when both `throws` and `async` are used?**  
Always write `async` before `throws`.

```swift
func loadData() async throws -> Data
let data = try await loadData()
```

---

**Q6. In which contexts can async functions be called?**  
1. Inside another async function.  
2. Inside an `@main` async entry point.  
3. Inside a `Task {}` or `DetachedTask {}` closure.

---

**Q7. What’s the difference between `Task.sleep(for:)` and `Thread.sleep()`?**  
`Task.sleep(for:)` suspends cooperatively without blocking the thread,  
while `Thread.sleep()` blocks the thread entirely.

---

✅ **Summary**
Swift’s async/await system enforces clear suspension points, cooperative task scheduling, and strict separation between async and sync code — ensuring responsive, safe, and predictable concurrency.

---
