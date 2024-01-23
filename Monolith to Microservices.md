> Objective: 
> One of my current projects is to transform monolithic software to microservices without downtime. I want to get to know some ways to achieve it and get some advice from this book.

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