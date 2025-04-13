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
    - High write frequency → requires a write-optimized database (e.g., Cassandra)