> Objective: To catch concept of coroutine before diving into coroutine
>
> Reference:
> - https://github.com/Kotlin/KEEP/blob/master/proposals/coroutines.md
> 

## Usecases of Coroutines

- Asynchronous computations → suspended execution
- Generators → lazy computation

## Terminology

- Coroutine: Instance of suspendable computation
    - not bound to any particular thread (differ from threading)
    - it can suspend its execution in a thread, and then resume in another thread
- Suspending function: A function marked with `suspend` modifier
    - enable an execution to be suspended without blocking the current thread
    - other suspending functions or lambdas can only invoke of it
- Coroutine Builder: A function that takes some suspending lambdas as arguments and creates coroutine
    - e.g. `launch{ }`, `future{ }`, `sequence{ }`
- Suspension point: A point where the execution may be suspended during coroutine execution
- Continuation: A state of suspended coroutine

## Suspending Function

- Needs to put `Continuation.resumeWith()` method in `suspendCoroutine` method as a call back (suspending function)
    
    ```kotlin
    suspend fun mySuspendFunction() {
        println("Before suspension point")
        suspendCoroutine<Unit> { continuation ->
            someAsyncOperation { result ->
                continuation.resumeWith(Result.success(Unit))
            }
        }
        println("After suspension point")
    }
    ```
    

## Coroutine Builder

- Suspending functions cannot be invoked from regular functions
- By using implementations of builder (e.g. `launch{ }`), we need to call `startCoroutine()` method of given suspend block

## Coroutine Context

- Persistent set of user-defined objects that can be attached to the coroutine
(like thread-local variables in threading)
- Coroutine context is immutable (whereas thread-local variables are mutable)
- `Element` of `CoroutineContext` is also a context itself

## Continuation Interceptor

- Offers an option to intercept and wrap the continuation of executions in a coroutine
    - e.g. make all executions run in a same thread

## Restricted Suspension

- `Sequence{ }`, `yield{ }` for lazy executions
- Uses different method (`createCoroutine()`) and it returns initial state of continuation (enable it to be lazy)