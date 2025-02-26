## CH02. Java Memory Areas and Memory Overflow

- Runtime Data Area
    - Program Counter (PC) Register
        - bytecode line number indicator of current thread
        - located at thread private memory (independent memory of each thread)
    - JVM Stack
        - push stack frame into JVM Stack when a method starts
        - pop stack frame from JVM Stack when the method ends
        - size of local variables is fixed during compile time
    - Native Method Stack
        - similar to JVM Stack
        - external library codes (os, hardware, ...)
    - Java Heap
        - shared by all threads
        - almost of instances are stored at heap
        - managed by gc
    - Method Area
        - shared by all threads
        - constants, static variables, ...
        - runtime constant pool (strings, literals, ...)

## CH03. Garbage Collector & Memory Allocation Strategy

- Key Questions
    - Which memory to collect
    - When to collect
    - How to collect
- Checking whether target is dead
    - it does not use Reference Counting Algorithm because of drawback on circular reference
    - instead, uses Reachability Analysis Algorithm
        - starting nodes are called GC root
        - candidates of GC root
            - objects that are referenced by JVM Stack
            - objects that are referenced as a static variable
            - objects that are used as a constant
            - objects that are locked with synchronized keyword
    - types of references
        - strong reference: usual reference => not a target of gc
        - soft reference: useful but not neccesary => can survive until memory is available
        - weak reference: weaker than soft reference => will be collected at next gc
        - phantom reference: the weakest reference
- Garbage Collection Algorithms
    - Generational Collection Theory
        - modern GCs are based on this theory
        - hypothesis
            - weak generational hypothesis: most objects die early
            - strong generational hypothesis: more GC iterations that objects survive, more possibilities they survive more
            - number of references between generations (old gen, young gen) is less than references in each generation
    - Mark and Sweep Algorithm
        - mark objects to be collected, then collect them
        - drawbacks
            - efficiency is irregular (depends on the number of objects)
            - memory fragmentation
    - Mark and Copy Algorithm
        - divide memory into same size of two spaces
        - move survived objects from one space to the other space
        - drawback
            - memory size decreases to a half
        - according to a research, 98% of objects in new generation could not survive
        => use eden space (80%) and two survival spaces (each 10%)
            - use eden space and one survival space => after gc survived objects are moved to the other survival space
    - Mark and Compact Algorithm
        - mark, move, collect
- Classic GCs
    - Serial Collector: Single thread
    - ParNew Collector: Uses multi-thread for collecting
    - CMS Collector: Minimizes stop-the-world time
        - initial mark → concurrent mark → re-mark → concurrent sweep
        - initial mark and re-mark requires stop-the-world
    - G1 Collector
        - collects memory by specific region (not by all new gen, all old gen, …)
        - uses humongous region to handle large objects
        - purpose of each region can be changed by gc
        - checks trash value of each region (trash value includes to-be available space of the region and expecting time to collect)
        - collects memory of region where the trash value is the highest
        - uses bi-directional remembered set (target it references and target it is referenced)
        - during concurrent marki phase, new objects are allocated at a new region (higher memory address than the others)
        - TAMS pointer handles the highest memory address
        - initial mark → concurrent mark → re-mark → copy and sweep
            - TAMS pointer is reassigned during initial mark phase

## CH06. Class File Structure

- Language on JVM → Language Compiler → Bytecode (class file) → JVM reads
- Structure
    - types
        - unsigned number: basic data types
            - u1 (1 byte), u2 (2 byte), …
        - table: composite data type, composed of several unsigned numbers and another tables
    - class file starts with magic number of 4 bytes
        - indicates it is jvm-allowed file (`0xCAFEBABE`)
    - minor and major version of JDK follows
        - 2 bytes of minor version and 2 bytes of major version
    - next, indicates the size of constant pool (2 bytes)
        - starts with index number 1 (index number 0 exists for reference check)
    - access flag
        - indecates it is class or interface or public or abstract, …
    - class index, parent class index, interface index
        - interface index is set of u2 data type (multi-implementation is allowed)
    - field table
        - explains variables in a class or interface
    - method table
    - attribute table
        - code attribute
            - body of method
            - size of bytecode command is u1 (1 byte), which means jvm uses maximum 256 commands (now it’s around 200)
            - default argument size of instance code is 1 (`this`)
            - static is 0 (no need to use `this`)

## CH07. Class Loading Mechanism

- Process
    - Loading → Verification → Preparation → Resolution → Initialization → Using → Unloading
        - Verification + Preparation + Resolution = Linking
    - Start of Loading, Verification, Preparation, Initialization, Unloading must be sequential
- Loading
    - find and get binary byte stream exactly matching with given class
        - there is no more guideline for this step 
        ⇒ increased degree of freedom to implement this step
        - loading from zip, jar, war, ...
        - loading from network
        - generating dynamically during runtime (dynamic proxy)
    - transform static storage structure expressed as byte stream to runtime data structure
    - make `java.lang.Class` object, which expresses loading target class, in heap memory
        - application uses this object to utilize data stored in method area

## CH07. Design a Unique ID Generator in Distributed Systems

- Approaches
    - Multi-master replication
        - each database server increases next ID not by 1, but by K (K is the number of servers)
        - Hard to scale out (multiple datacenters, multiple servers)
        - IDs do not go up with time across servers
    - UUID
        - uses system-generating UUID (128 bit)
        - does not go up with time
        - non-numeric
    - Ticket Server [[reference](https://code.flickr.net/2010/02/08/ticket-servers-distributed-unique-primary-keys-on-the-cheap/)]
        - web servers use one single ticket server to get incremented ID
        - single point of failure
    - Twitter Snowflake Approach
        - divides ID into several sections
            - Sign bit
            - Timestamp: includes miliseconds
            - Datacenter ID
            - Machine ID
            - Sequence Number: increments by 1 on each machine/process and is reset to 0 every milisecond
## CH11. Backend Compile and Optimization

- Method inline
    - remove method call and use the value directly
        
        e.g. `x = b.get()` → `x = b.value`
        
- Redundant store elimination
    - replace call for same value to use already-allocated value
        
        e.g. `z = b.value` → `z = x`
        
- Copy propagation
    - remove useless local variables
        
        e.g. `sum = x + z` → `sum = x + x`
        
- Dead code elimination
- Escape analysis
    - degree of escape
        - GlobalEscape: an object can access another thread
        - ArgEscape: an object is delivered or referenced by another, but do not escape globally
        - NoEscape: an object only lives in a method
    - decision making to choose whether it needs to allocate an object in heap or not
        - using heap is an overhead for gc
    - stack allocation
        - if an object is not GlobalEscape, it does not have to use heap
    - scala substitution
        - substitue an object to scala form (atomic form)
    - synchronization elemination
- Common subexpression elimination
    - static, already-analyzed expressions are replaced with the outcome of the analysis
- Array bound checking elimination
- …

## CH12. Java Memory Model and Thread

- in hardware
    - uses cache between processor and memory
    - provokes cache coherence issue
- in jvm
    - defines own memory model to be compatible with various os and hardwares
    - each java thread has its own task memory
        - they can not access directly another one's task memory (must pass through main memory)
    - methods to control between task memory and main memory
        - lock
        - unlock
        - read
        - load
        - use
        - assign
        - store
        - write
    - volatile: uses the most recent value of variable in main memory
        - only guarantees visibility
        - prevents command from re-sorting
        - but also prone to concurrency issue
- JVM
    - depends on jdk version
    - but usually uses kernel thread
        - high context switching overhead
    - also uses preemptive scheduling
    - coroutine emerged, and virtual thread came up

## CH13. Thread Safe and Lock Optimization

- What is Thread-Safe?
    - Doesn’t have to consider particular thread scheduling or alternative running method
    - No need to additional synchronization method or coordination by caller
- Degree of Thread-Safe
    - immutable
        - e.g. final
    - absolute thread-safe
        - no need to additional synchronization method by caller
    - conditional thread-safe
        - usual concurrent classes (e.g. Vector, ConcurrentHashMap, …)
    - thread-compatible
    - thread-unsafe