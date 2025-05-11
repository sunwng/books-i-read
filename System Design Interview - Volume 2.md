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