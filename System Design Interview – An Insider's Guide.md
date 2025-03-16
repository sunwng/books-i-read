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

## CH06. Design a Key-Value Store

- Single server key-value store
    - using in memory hash table ⇒ intuitive approach
        - fast but not scalable
- Distributed key-value store
    - need to consider CAP theorem
        - Consistency, Availability, Partition Tolerance
    - in real world, partition tolerence cannot be avoided
    - data partition ⇒ use consistent hashing
    - data is replacated asynchronously over N servers
    - how to meet consistency
        - write operation is considered successful when it is acknowledged from N replicas
        - read operation is considered successful when it gets responses from at least N replicas
    - needs to resolve data conflicts
        - can be handled using vector clock
- failure detection
    - all to all multicasting ⇒ inefficient
    - each node randomly checks another node’s heartbeat
- handle temporary failures
    - use hinted handoff (down node is temporarily replaced by another alive node)
- handle permanent failures
    - keep data synchronously stored in different data centers
- Additional Studies
    - InnoDB (e.g. MySQL)
        - auto-increment option ensures unique ID usually
        - it uses data structure to manage uniquely incrementing ID for each table in memory
        - earlier transaction takes lock of the data structure and uses the next incremented ID
    - MongoDB
        - UID is not generated by DBMS server
        - each process having MongoDB client generates it
        - structure
            - Unix timestamp ⇒ 4 bytes
            - random value to identify machine ⇒ 3 bytes
            - random value to identify process ⇒ 2bytes
            - counter (initialized with random value) ⇒ 3bytes
            
            = 12 bytes

## CH08. Design a URL Shortener

- Process
    1. Client → (short url) → url shortner server
    2. url shortner server → (long url with 301 or 302 status) → Client
    3. Client → (long url) → business server
- Method to make a short url
    - use hash function
        - needs to check how many hash values the function must generate
    - or base62 (number + all english alphabet) conversion can work
        - unique numeric id : base64 string : logn url = 1 : 1 : 1

## CH10. Design a Notification System

- Notification server can be connected with multiple third-party systems (APN, FCM, SMS, Email, …)
    - those systems can make a bottleneck (maximum cap of throughput)
    
    ⇒ use message queues for each system
    
- It needs to prevent data loss
    - before entering message queue, persist it in a database marked with `not-processed`
    - after sending notification, update it as `processed`

## CH11. Design a News Feed System

- Feed Publishing
    - at post service, persist post in both db and cache
    - at feed service, update related friends’ feed cache
        - feed cache maps `user_id` to `post_id`
    - at notification service, notify to related friends
- Feed Building
    - at feed service, fetch news feed cache
- Process
    1. Publish API
        - uses persisting job in post service
        - after persisting it publishes an event (published event)
        - API returns OK
    2. Feed service consumes the published event
        - find related users from graph db
        - update feed cache
    3. Notification service consumes the published event
        - notification flow processes
- Cache
    - To meet high responsiveness, needs to use various cache
    - feed, content (hot content is seperately managed), social graph, actions, likes

## CH12. Design a Chat System

- Options to implement chatting
    - Polling ⇒ a lot of useless connections
    - Long Polling
    - WebSocket ⇒ most simple
- Other specs can be implemented using APIs
- Which db is suitable for a chat history data
    - Key-Value store
        - fast reading
        - horizontal scaling
    - Data structure
        - 1 : 1 ⇒ message_id
        - group ⇒ channel_id + message_id
- What if a recipient is offline or users have multiple devices
    - we can use current maximum id of message for each device of each user
    - and compare between maximum id from kv store and current maximum id, then sync
- How to deal with user disconnection
    - network connections usually disconnect and reconnect frequently in a short time
    - we can use hearbeat from user to check connection
    - presence server gets hearbeat request and manages connection status of user
- Design differs significantly depending on whether it is for a small group chat or a large group chat

## CH13. Design a Search Autocomplete System

- Optimal data structure & strorage
    - key: value & RDB ⇒ inefficient for querying with like condition
    - Trie data structure looks suitable
        - we can find each subnode’s frequency sum
        - Redis, Elasticsearch, ArangoDB, …
- Services involved
    - Data gathering service
        - manages how many times each string is queried
        - updating trie in real time is high load
        - brief process
            - read search logs periodically
            - aggregate data of the interval
            - update trie based on the last updated one
        - If it needs almost-real-time trie, we can shorten the interval
            - or adopting a streaming job (e.g. kafka streams) to replace aggregating job
    - Query service
        - finds top N query strings using like condition with input string
        - we can cache the most recent result of each node

## CH14. Design Youtube

- Uploading flow
    1. Transcode video at transcoding servers
    2. When it completes, upload to transcoded storage (cdn)
    3. publish upload-completed event
    4. API servers consume the event and update metadata table and cache
- Video streaming
    - Usually streaming needs another protocol (Microsoft Smooth Streaming, Adobe HDS, …)