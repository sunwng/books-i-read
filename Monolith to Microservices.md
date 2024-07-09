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