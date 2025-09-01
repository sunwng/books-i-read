## CH01. Proximity Service

- Search API
    - Parameters: latitude, longitude, radius
    - How to index and search location information efficiently?
        - Uniform Grid: simple, but real-world distribution is often uneven
        - Geohash:
            - recursively divides regions and converts 2D coordinates into 1D strings
            - offers up to 12 levels of precision
        - Quadtree:
            - recursively divides space into four quadrants
            - requires a memory-based data structure
- Read/Write Ratio
    - Reads occur more frequently than writes
        - writes only happen when business information changes
- Database design
    - Geohash ⇒ save seperate records (geohash, business_id)

## CH02. Nearby Friends

- The user needs to receive real-time location updates of all nearby friends
- A WebSocket server is used to handle these updates
    - Redis Pub/Sub is integrated to broadcast location update events
    - As this is a stateful server, proper handling of node removal is required
- Redis Pub/Sub
    - 1 users - 100 nearby friends = 100 channels per user needed
    - needs to distribute channels and sharding (based on event publishing user ID)
- Additional Study
    - Redis Pub/Sub does not persist event
    - Redis Pub/Sub does not check response confirm

## CH03. Google Maps

- Navigation Algorithm
    - Requires a graph-based data structure
    - Use separate graphs to handle size sensitivity
- Location Service
    - Clients send user locations in batches
    - Requires high availability over consistency
    - High write frequency → requires a write-optimized database (e.g., Cassandra)
        - Cassandra does not use an *in-place update* strategy
        - Instead, it uses a *Log-Structured Merge Tree (LSM-Tree)*
    - We can produce events related to updated locations, and services that use location data can consume them (e.g., via Kafka)
- Navigation Service
    - Needs to generate waypoints
    - Shortest n-pathes can be found using the A* algorithm
    - ETA can be calculated using a machine learning model trained on historical data

## CH04. Distributed Message Queue

- Do not consider event streaming platform such as Kafka
- Advantages of using message queue
    - decoupling
    - improved scalability
    - improved avalability
- Message models
    - Point-to-point
    - Publish-Subscribe
- Three possible strategies
    - At most once
        - messages may be lost, but are not redelivered
        - producer sends a message without waiting for an acknowledgement (`ACK = 0`)
        - consumer commits the offset before its process is successfully done
        - suitable for cases that small amount of loss is acceptable (e.g. monitoring)
    - At least once
        - no message should be lost, but can be redelivered
        - producer sends a message with `ACK = 1` or `ACK = all`
            - it retries on timeout or failure
        - consumer commits the offset after its process is successfully done
        - suitable for cases that data duplication is not a critical issue or identifying redelivered message is possible
    - Exactly once
        - producers need to be idempotent
        - consumers fetch commited messages only
        - [case of kafka](https://www.confluent.io/blog/exactly-once-semantics-are-possible-heres-how-apache-kafka-does-it/)
- How to persist messages
    - Heavy writes and heavy reads
    - Using DB ⇒ hard to satisfy both write and read requirements
    - Write-Ahead Log (WAL)
        - pure sequential read/write ⇒ a good option
        - rotational disks are slow only for random access
- Producer flow
    1. Send messages to the routing layer
    2. Read the replication strategy from metadata storage
    3. Routes the messages to the leader broker
    4. The leader broker receives the messages and replica pull the data
    5. Check whether a sufficient number of replicas have synchronized, then commit
- Consumer flow
    - Strategy
        - Push
            - low latency
            - if the rate of consumption is below the rate of production, it will be overwhenlmed
            - it is difficult for consumers to prepare computing resources
        - Pull
            - the rate of consumption is determined by consumers
            - even though there are no messages in the broker, consumers still keep pulling, wasting resources
        
        ⇒ Pulling strategy is usually chosen
        
- State storage
    - stores
        - mapping b/w partitions and consumers
        - last consumed offsets of consumer groups for each partition
    - data access patterns
        - random and frequent read & write
        - frequently updated, rarely deleted
- Broker
    - the higher the minimum number of ISRs (In-Sync Replicas), the safer
        - but needs to consider tradeoff b/w latency and safety

## CH05. Metrics Monitoring and Alerting System

- Five fundamental components
    - Data collection
    - Data transmission
    - Data storage
    - Alerting
    - Visualization
- Metrics collection
    - pull vs push
        - pull: metrics collector periodically pulls metric values from the running applications
            - difficult to maintain in large-scale environments (servers are added and removed frequently)
            - but, this drawback can be mitigated by using service discovery components
            - easy to perform health checks
        - push: applications sends metrics directly to metrics collector via an collection agent installed on each server
            - lower network latency (usually uses UDP, while pull model uses TCP)
        
        ⇒ each models have reasonable pros and cons
        
- Data access pattern
    - heavy write
    - spiky read
- Data storage system
    - needs to effectively handle time series data
    - candidates are Cassandra, InfluxDB, etc …
    - we can seperate the storage into hot and cold
- Alerting
    - read config and periodically query whether any value exceeds a defined threshold

## CH06. Ad Click Event Aggregation

- Correctness is important because the data is used for ad billing
- Requirements
    - Aggregate the number of clicks for specific ad (`ad_id`) in the last `M` minutes
    - Return the top `N` most clicked ads in the last `M` minutes
    - Support aggregation filtering by different attributes
- Storing data
    - it is safe to store both raw click event data and aggregated result data
    - raw data ⇒ for debugging and backup
    - aggregated result data ⇒ for query efficiency
- Database
    - write oriented system
    - Cassandra can be great due to heavy writes and timestamp-based data structure
- Architecture
    - click events are produces using Kafka
    - data aggregation service consumes them
        - use MapReduce framework to efficiently process the data
    - it calculates aggregated results and publish the results
    - a recording service consumes the results and save them to database
- Timestamp
    - can be generated during
        - event time
        - processing time
    - due to asynchronous processing architecture, gap b/w event time and processing time can be large
    - using event time ⇒ there can be a delay
    - using processing time ⇒ it can be not accurate
        - to handle this problem, we can use watermark method (giving extension of aggregation window)
        - size is usually determined heuristically, and it is not an absolute solution

## CH07. Hotel Reservation System

- Database
    - To ensure consistency and avoid concurrent issue, ACID is important
    - Data structures for Hotel, Room, Rate, Reservation are well-suited to relational modeling
    
    ⇒ RDB can be a good choice
    
- Architecture
    - Use caching for hotel data, as it is static
    - We can use gRPC for inter-microservices communications

- Concurrency issues
    - multiple clicks of same user
        
        ⇒ make API idempotent
        
    - multiple requests on same room
        - needs to consider isolation level of database
        - Pessimistic lock, Optimistic lock (version number, timestamp, …)
- Eventual consistency
    - 2-phase commit
    - Saga

## CH08. Distributed Email Service

- Email knowledge
    - protocols
        - SMTP (Simple Mail Transfer Protocol) for sending
        - POP (Post Office Protocol) for receiving
        - IMAP (Internet Mail Access Protocol) for receiving
- Choosing database
    - RDB ⇒ not suitable for large email data (also for search something in blob)
    - NoSQL
        - Requirements
            - a single column can be a single-digit of MB
            - strong data consistency
            - reduce Dist I/O (full scan)
- Search
    - use Elasticsearch
        - we need to sync Elasticsearch with our primary email store

## CH09. S3-like Object Storage

- Storage system 101
    - Block storage
        - common storage devices that are physically attached to servers
    - File storage
        - has hierarchy
    - Object storage
        - no hierarchy, immutable
- Search object id by name of it, then find object with its id
- Data Store Service
    - Data Rouiting Service
        - query the Placement Service to get the best data node to store data
        - read data from data nodes
        - write data to data nodes
    - Placement Service
        - it determines which data nodes should be chosen to store an object
        - manages virtual cluster map, which contains location information
        - continuously monitors all data nodes through heartbeats
        - commonly uses consistent hashing
    - Data Node
        - stores the actual object data
- How data is organized
    - storing small files seperately consumes a lot of blocks and inodes
    - it is better to merge small objects into a large file (like Write Ahead Log)
    - UUID-Object mapping should include the name of file and start offset
    - we can use erasure coding method to ensure data durability
        - but it makes larger latency (slower responsibility)
- Metadata data model
    - requirements
        - find the object ID by object name
        - insert and delete an object based on the object name
        - list objects in a bucket sharing the same prefix
    - bucket
        - `bucket_name`
        - `bucket_id`
        - `owner_id`
    - object
        - `bucket_name`
        - `bucket_id`
        - `object_name`
        - `object_id`
    - needs sharding
        - by `bucket_id` ⇒ prone to hot spot bucket
        - by `object_id` ⇒ not applicable (queries are based on their `_name`)
        - by combination of `bucket_name` and `object_name` ⇒ reasonable
            - to handle complicated implementation for the listing requirement, we can utilize seperate table, sharded by bucket_id, only for the listing

## CH10. Real-time Gaming Leaderboard

- Using message queue to update scores in leaderboard
    - if we use message queue, we can propagate the score update to others easily
- Database
    - Relational database
        - racords look like `(user_id, score)`
        - querying becomes very slow when there are millions of users
    - Redis
        - we can use sorted set datatype of redis
            - it uses a hash table and skip list internally
            - uses a hash table to map a member (unique key) to its score (value) with `O(1)` complexity
            - uses a skip list to keep all members sorted by their scores
                - has multiple layers of hierarchy
                (higher layers have fewer nodes, and the lowest layer contain all nodes as a sorted linked list)
                - if a score changes, the corresponding node is removed from the skip list and re-inserted at the proper new position
                - the complexixty of searching a range of M elements is `O(logN + M)`
        - increase score with `ZINCRBY`
        - fetch top users with `ZREVRANGE`
        - fetch rank of specific user with `ZREVERANK`
        - we create a new leaderboard sorted set every month
        - move previous one to historical data storage
        - it is difficult to consider sharding
            - hash partition → complex approach to fetch top10 users and calculate exact ranking of specific user (scatter and gather)
            - fixed partition → need to move a partition of specific user when the score is updated
    - NoSQL
        - requirements
            - optimized for writes
            - efficiently sort items
        - candidates are dynamodb and mongodb
        - use partition key to distribute data (n partitions for specific month)
        - use global secondary index to sort (score)
        - needs scatter and gather application level process

## CH11. Payment System

- Reconcilation process between internal and external services is important
- Ledger
    - financial record of payment transaction
- Pay-in flow
    1. User clicks order button
    2. Payment service stores payment event in its own database
    3. Payment executor stores payment order in its own database
    4. Payment executor calls an external PSP
    5. Payment service updates the wallet
    6. Wallet service stores the updated balance information in its own database
    7. Payment service updates the ledger
    8. Ledger services appends the new ledger information in its own database