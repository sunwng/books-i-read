> Objective: Get to know how parallelism works in java and use it in a right way
> 

## CH01. Introduction

- Most modern operating systems use threads (not a process) as a basic unit of scheduling

## CH02. Thread Safety

- Writing thread-safe codes ⇒ Managing access to state (shared and mutable)
    - in Java, by using `synchronized` keyword (primary mechanism for synchronization)
- Heart of the definition of Thread-Safety is **“Correctness”**
    - Correctness means that a class conforms to its specification
    - and good specification defines
        - invariants constraining an object’s state
        - postconditions describing the effects of its operation
    
    ⇒ If a class behaves correctly regardless of scheduling, etc, then it is thread-safe
    
- Stateless objects are always thread-safe
- Atomicity
    - to avoid a race condition, it needs to make read-modify-write process atomic
        
        ⇒ Compound Actions (can use Atomic Variables in `java.util.concurrent.atomic`)
        
- Locking
    - What if a behavior needs two or more shared states? → use Lock (w/ Critical Section)
    - use `synchronized` keyword to make a whole method (behavior) atomic
        - only one thread can access
        - (use a same lock in a same class)
    - or use `synchronized(lock){}` to make a certain critical section in a method
        - also, can use different locks in a same class

## CH03. Sharing Objects

- We always need to use a proper synchronization whenever a thread is shared across threads
    - w/o synchronization, a variable can be seen as stale
- `volatile` ⇒ is not cached in registers
    - always returns the most recent write by any thread
    - only guarantees visibility (↔ Lock guarantees both visibility and atomicity)
- Thread Confinement
    - use local variables (stack confinement)
    - use ThreadLocal (uses heap memory)
        - `ThreadLocal.get()`, `ThreadLocal.set()`
- Immutable variables (`final`) are always thread-safe

## CH04. Composing Objects

- Recognizing a role of objects is important to design thread-safety (OOP)
- Delegating thread-safety
    - use synchronized classes instead of making a method synchronized
        
        (e.g. AtomicLong, ConcurrentHashMap, …)
        
- Need to consider synchronization of callers
- Also consider synchronization of a reference that an object have as a dependency