> Objective: 
> One of my current projects is to transform monolithic software to microservices without downtime. I want to get to know some ways to achieve it and get some advice from this book.

## CH01. Just Enough Microservices

- Microservices
    - **independently deployable** services modeled around a business domain
        - services should be loosely coupled
    - communicate w/ each other **via networks**
        - needs to think about latencies, transactions
- Monolith
    - Single Process Monolith
    - Modular Monolith
        - divided into modules, but needs to be deployed together
- Coupling & Cohesion
    - implementation coupling (technologically coupled)
    - temporal coupling (because of synchronous communication)
    - deployment coupling
    - domain coupling

## CH02. Planning a Migration

- Understanding a goal
    - There must be something to achieve by doing migration
- Importance of incremental migration
    
    > *If you do a big-bang rewrite, the only thing you’re guaranteed of is a big bang.*
    — Martin Fowler
    > 
    - Quick iterations are important as it makes wrong migration reversible
- Decomposing an existing monolithic system
    - Domain-Driven Design
        - helps to define boundaries of services (bounded contexts)
    - Event Storming
        - w/ tech and non-tech stakeholders
        - domain events ⇒ aggregates ⇒ bounded contexts
    - Knowing dependencies b/w domains
        - can figure out which domain is easier to extract than others

## CH03. Splitting the Monolith

- Leaving functionality in monolith gives us more options
    - rollback point, validation, parallel implementation, …
- Rewrite only small pieces of functionality at a time and ship it to customer regularly
- Migration patterns
    - Strangler Fig pattern
        1. Identify a target from the existing system
        2. Implement it in a new service
        3. Re-route (we can call both to validate the functionality)
        - it doesn’t work too well when the functionality to be moved is deeper inside inside the existing system
        - we can use feature toggle to control where to route
    - Changing behavior while migrating functionality
        - it makes it difficult to rollback to original system
        - migrating completely and then adding new feature is standard
    - Branch by Abstraction pattern
        - when the migration target is deeper inside the monolithic system
        1. Create an abstraction for the functionality to be replaced
        2. Change codes to use it
        3. Create a new implementation of it to call a new microservice
        4. Control traffic b/w them (feature toggle, parallel-running validation, …)
    - Decorating Collaborator pattern
        - add more functionalities on proxy
        - it can modify the response to customer (not use same response from the monolith)
        - it can do something more after getting response from the monolith
    - Change Data Capture pattern
        - it can be achieved with database trigger or using transaction log

## CH04. Decomposing the Database

- Splitting the database first, or code?
    - Splitting the database first
        - Need to use in memory join from schemas
        - Unlikely to yield short-term benefit
            - because of the deployment
        - Only when performance and consistency issues are the most important
    - Splitting the code first
        - It is much easier to understand what data is needed by the new service
        - Can have a benefit of independent deployability
        - But, usually leave the database shared, only split the code
- Schema Separation
    - Make a new schema for a new service, and maintain the view (same as before) for other services to use
- Database Wrapping Service
    - It can be used when a schema is a hot spot from a lot of applications