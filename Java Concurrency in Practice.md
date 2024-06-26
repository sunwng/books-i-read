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

## CH05. Building Blocks

- Even with delegation (e.g. using Vector), it is sometimes necessary to use client-side locking
    - example
        
        ```kotlin
        // not thread safe
        for (int i = 0; i < vector.size(); i++)
        	doSomething(vector.get(i));
        ```
        
- Locking collection for a long time (e.g. for iteration) is not good for application
    - it may occurs `ConcurrentModificationException`
- Concurrent Collections
    - Synchronized collections show low throughput (because of serialization of threads)
    - `ConcurrentHashMap`
        - uses lock striping mechanism (allow a greater degree of shared access)
    - `CopyOnWriteArrayList`
        - derives thread safety from the fact that as long as an effectively immutable object is properly published, no further synchronization is required
    - `BlockingQueue`
        - A thread calling `put` method blocks until space becomes available
        - A thread calling `take` method blocks until an element is available
- Blocking method can generate `InterruptedException`
- Synchronizers
    - Latches → Delays the progress of threads until it reaches terminal state
    - FutureTask → Synchronization during async job
    - Semaphores → Controls the number of threads it can be accessed
    - Barriers → Waits until all threads come together at a barrier point

## CH06. Task Execution

- Parallelism can be met only if tasks are independent
- Disadvantages of unbounded thread creation
    - Thread lifecycle overhead (overhead by creating new thread)
    - Resource consumption (More threads, more heap memory usage)
    - Stability (Lack of memory makes application down)
- Executor
    - works as producer-consumer
- Thread Pool
    - tightly bound to work queue
    - reuse used thread

## CH07. Cancellation and Shutdown

- When InterruptedException occurs
    - throws it to caller
    - call `interrupt` method to stay interrupted
- `RuntimeException` → kills its thread
    - we can use `UncaughtExceptionHandler` to handle `RuntimeException`
- Threads
    - general thread → from main thread
    - daemon thread
        - JVM does not call finalize method in daemon thread when it shuts down

## CH08. Applying Thread Pools

- If a job is executed with dependency on another job in a thread pool → possibility of deadlock
- Size of thread pool → needs to be dynamically controlled
- ThreadPoolExecutor
    - arguments: corePoolSize / maximumPoolSize / keepAliveTime / workQueue (`BlockingQueue`) / threadFactory
    - Three strategies of worker queue
        - no size limit (`NewFixedThreadPool`, `NewSingleThreadExecutor`)
        - has size limit
        - not using queue (`SynchronousQueue`) ⇒ only when it has a lot of threads
- Saturation Policy
    - policy used when queue is full
    - e.g. `AbortPolicy`, `CallerRunsPolicy`, …
    - `RejectedExecutionHandler` handles this situation with given policy
    - Default is `AbortPolicy` and it throws `RejectedExecutionException`
- or we can use Semaphore to control size of queue

## CH10. Avoiding Liveness Hazards

- Deadlock
    - multiple threads wait forever due to a cyclic locking dependency
- Not like dbms, JVM does not help programs to resolve a deadlock
- Lock-Ordering Deadlocks
    - Using N locks, methods should check and get locks in a same order
    (1st Lock, 2nd Lock , …)
    - can occur by between objects
- Avoiding and diagnosing deadlocks
    - Use timeout (`tryLock`)
    - Analyze with thread dump

## CH11. Performance and Scalability

- First make your program right, then make it fast
- Scalability
- : Ability to improve throughput or capacity when additional computing resources are added
    - Many of tricks that improve performance in single-threaded program are bad for scalability
- Costs introduced by threads
    - Context switching
- Memory synchronization
    - JVM optimizes useless locks
- Blocking
    - JVM can implement blocking via
        - spin waiting (repeatedly trying to acquire the lock until it succeeds)
        → preferable for short waits
        - suspending (needs help of OS) → preferable for long waits
- Reducing lock contention
    - principal threat to scalability in concurrent applications is the exclusive resource lock
    - two factors can affect lock contention
        - how often the lock is requested
        - how long it is held once acquired
    - Narrowing lock scope → holds lock as briefly as possible
        - e.g. use `synchronized` block instead of making a whole method synchronized
    - Reducing lock granularity → lock splitting or lock striping 
    (using separate locks instead of using one)
        - e.g. use `synchronized` block with different locks
        
## CH12. Testing Concurrent Programs

- Potential failures may be rare probabilistic occurrences
- Testing for correctness
    - check whether it throws `InterruptedException`
    - use `Thread.isAlive()` to check whether it is waiting (or `Thread.isInterrupted()`)
    - use `ExecutorService` to make a case when multiple threads are present
    - useful classes for testing
        - `CountDownLatch` → wait on the main thread
        - `CyclicBarrier` → wait on each thread
    - use `Thread.yeild()` to make context switching