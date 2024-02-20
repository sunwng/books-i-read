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
    
    ![Untitled](https://miro.medium.com/v2/resize:fit:4800/format:webp/1*BMsNAGvTkaLoeD7X819N1A.png)
    
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
- Sending a message synchronously
    - A thread must wait until it gets response from a broker
    
    ⇒ not common
    
- Sending a message asynchronously
    - can use a callback method to handle errors from a broker
        
        → implement `org.apache.kafka.clients.producer.Callback`
        
        → pass the implementation to `send()` method
        
- Configuring Producers
    - client.id
        
        ⇒ to distinguish producers
        
    - acks
        
        ⇒ determine the number of partition replica that receives the record
        
        - `acks=0` : Don’t care whether any replica receives the record or not
        - `acks=1` : Count as success when the a leader replica receives the record
        - `acks=all` : Count as success when all in-sync replicas receive the record
    - message delivery time
        - two sections of it
            - calling of send() ~ returning of result
            - returning of result ~ call of callback
        
        ![Untitled](https://www.conduktor.io/kafka/_next/image/?url=https%3A%2F%2Fimages.ctfassets.net%2Fo12xgu4mepom%2F18IoNzy4ocy0kMdyE5I4AE%2F023857fec700bdf8ff59d8971e5ce499%2FKafka_Producer_Retries_Delivery_Timeout_Process.png&w=3840&q=75)
        
        - `max.block.ms` : Determines maximum time that the producer can be blocked
        - `delivery.timeout.ms` : Determines maximum time between after returning of send() and before receiving a response from a broker
            
            → delivery.timeout.ms ≥ linger.ms + request.timeout.ms
            
        - `request.timeout.ms` : Determines maximum time to wait the response from the broker
        - `retries` : Determines maximum number to retry sending to the broker
        - `retry.backoff.ms` : Determines waiting time between retries
        - `linger.ms` : Determines waiting time before sending the batch
        - `buffer.memory` : Determines maximum size of memory of waiting buffer
        - `batch.size` : Determines the size (byte) of batch
        - `max.in.flight.requests.per.connection` : Determines the maximum number of messages that can be sent even before receiving a response from a broker
        - `enable.idempotence=true` : Guarantees the idempotence of records
            - `max.in.flight.requests.per.connection` ≤ 5
            - `retries` ≥ 1
            - `acks = all`