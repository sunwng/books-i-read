> Objective: 
> 
> To know the inside of Kafka clients.
> 

## CH01. Meet Kafka

- Publish/Subscribe Messaging
    - Pub/Sub pattern is characterized by the sender not specifically directing it to a receiver
    - A lot of 1:1 connections make a system harder to trace
- Message
    - Unit of data within Kafka
- Batch
    - A collections of messages
    - The larger the batches, the more messages that can be handled per unit of time
        
        (Tradeoff b/w latency and throughput)
        
- Schema
    - A type of message content
    - e.g. Json, xml, avro, …
- Topic
    - Category of messages
    - Topics are broken down into a number of partitions
        
        (Each partition can be hosted on a different server)
        
- Kafka clients
    - users of the system
    - Producer
        - creates new messages
        - A message is produced to a specific topic
    - Consumer
        - reads messages
        - uses offset of messages to keep track of the messages
        - Consumers work as part of a consumer group
            - The group assures that each partition is only consumed by one member
- Broker
    - a single Kafka server
    - receives messages from producers
        
        → assigns offsets to them
        
        → commits the messages to storage on disk
        
        → responds to fetch requests from consumers