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