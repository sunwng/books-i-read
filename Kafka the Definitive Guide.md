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

## CH03. Kafka Producers: Writing Messages to Kafka

- Overview
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/e44a26ef-329f-4598-9429-3f4078727a56/6b4b080b-0a45-4e65-a1bb-1db85f98abff/Untitled.png)
    
    - Starts with generating an instance of `ProducerRecord`
        - target topic and value are necessary
        - key and target partition are optional
    - Call `Send()` API
        - serializes the data to byte array
    - `Partitioner` decides a target partition to send
        - adds a record to a batch of records that will also be sent to a same topic and partition
    - Another separate thread sends the batch to a Broker
- Constructing a Kafka Producer
    - Three mandatory properties
        - bootstrap.servers
            - lists of `host:port` of broker
            - no need to know the whole lists
        - key.serializer
        - value.serializer
- Three primary methods of sending messages
    - Fire and forget → Don’t care whether the message successfully arrives or not
    - Synchronous send → Wait until the message arrives or not
    - Asynchronous send → Use callback function, which is triggered when it receives a response from a Broker