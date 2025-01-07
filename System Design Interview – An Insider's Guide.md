## CH01. Scale from zero to millions of users

- database
    - usually, rdb is a best option
    - we can consider to use nosql on below conditions
        - needs very low latency
        - needs to store very large amount of data
        - requires only deserialization/serialization
- scale up vs scale out
    - scale up has deterministic drawbacks
        - there is an upper limit
        - no failover or redundancy
- cache
    - read-through caching strategy
        - read db only when cache miss occurs
    - things to consider
        - property of data (read intensive?)
        - importance of data (cache is volatile)
        - expiration of data
        - consistency of data

## CH02. Back-of-theE-Envelope Estimation

- reading disk is still slow whereas memory is fast
- simple compression is fast

## CH03. A Framework for System Design Interviews

1. Understand the problem and establish design scope
    - doing right and clear questions
    - make appropriate assumptions
    - get information to design a system
2. Propose high-level design and get buy-in
3. Design deep dive

## CH04: Design a Rate Limiter

- Control the rate of traffic sent by a client
- Benefits
    - prevent resource starvation caused by Denial of Service attack
    - reduce cost (if use paid third party APIs)
    - prevent servers from begin overloaded
- Where should it be implemented?
    - separate service => API gateway
    - in application code
- Algorithms
    - Token bucket
        - requests check if there is enough token in a bucket
        - refiller refills tokens periodically
        - two parameters (bucket size, refill rate) can be challenging to tune
    - Leaking bucket
        - uses queue instead of bucket
        - requests are polled from the queue at regual intervals
        - queue can be filled with old requests
    - Fixed window counter
        - counts requests in fixed size of time
        - there can be a burst of traffic at the edges of time
    - Sliding window log
        - fixes problem of fixed window counter
        - keeps track of request timestamps
        - outdated timestamps based on current request are removed
    - Sliding window counter algorithm
        - Fixed window counter + Sliding window log
            - uses fixed window and considers previous window
        - Considers previous window with following formula
            - considered requests from previous window = # of requests of previous window * (1 - time elapsed from the start line of current window / window size)

## CH05: Design Consistent Hashing

- Usually use hashing to distribute something (traffic, data, …)
    - When the number of distribution destination is changed → difficult to handle hard coded hashing (rehashing problem)
        - Consistent Hashing is to solve this problem
- Consistent Hashing
    - Make only k (number of keys) / n (number of slots) keys remapped on average when hash table needs to be resized
    
    (Traditional hash tables require all keys to be remapped)
    
- How it works
    - Collect lower end and higher end of hash keys and make it circle
    - Allocate slots (distribution destinationo) on the circle
    - When a hash key is given, find the nearest slot in a clockwise manner
    - When a slot is added or removed, only small portion of hash keys needs to be remapped
- Problems
    - If there was any adding or removing slot history, hash key partitions cannot be evenly distributed
    - Also, hash keys are easy to be not evenly distributed
    
    ⇒ By making virtual nodes (1 distribution destination can have N nodes), these two problems can be solved