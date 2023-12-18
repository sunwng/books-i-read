> Objective: 
>
> To build a good software, knowing various types of architecture is necessary. By studying this, want to choose a proper architecture while developing a software.
> 

## CH01. Introduction

- Architecture
    - hard to define
    - is about the important stuff
- Software Architect
    - in the past
        - only dealt with the purely technical aspects of architecture
        (e.g. modularity, components, patterns, …)
    - now
        - role of architect has expanded
        (e.g. metrics, styles, validations, …)
    - needs to know
        - structure of the system
        - architecture characteristics
            - characteristic that the system must supports 
            (e.g. availability, scalability, agility, …)
        - architecture decisions
            - define the rules for how a system should be constructed
        - design principles
            - guideline for developing a system
- Expectations of an Architect
    - Make architecture decisions
        - guide technology decisions (not choose a specific technology)
    - Continually analyze the architecture
        - continually analyze the architecture and current technology environment
        - then recommend solutions for improvement
    - Keep current with latest trends
        - understanding and following key trends helps the architect prepare for the future and make the correct decision
    - Ensure compliance with decisions
    - Diverse exposure and experience
    - Have business domain knowledge
        - w/o knowing business domain, it is difficult to make a decision
    - Possess interpersonal skills
    - Understand and navigate politics
- Laws of Software Architecture
    - Everything in software architecture is a “trade-off”
    - Why is more important than how

## CH02. Architectural Thinking

- Architectural thinking is much more than just thinking about the architecture
- Need to
    - know the difference b/w architecture and design
    - have a wide breadth of technical knowledge while still maintaining a certain level of technical depth
    - understand, analyze, and reconcile tradeoffs b/w various solutions and technologies
    - understand the importance of business drivers
- Architecture vs Design
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/e44a26ef-329f-4598-9429-3f4078727a56/3ed3449a-237c-4f1e-87ee-cf2c3ef6fb2b/Untitled.png)
    
    - in traditional view, it is the unidirectional arrow from architect to developer
    - this causes a lot of problems
        - because of virtual and physical barriers
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/e44a26ef-329f-4598-9429-3f4078727a56/87128a37-4ffd-4267-8e38-c6be4f7073fe/Untitled.png)
    
    - need to brake the barriers down, and make it bidirectional
    
    ⇒ there is no boundary b/w architecture and design
    
    (both part of the circle of life within a software project)
    
- Technical Breadth
    - There are three sections of knowledge
        - Stuff I know → developer’s early career focuses on this section
        - Stuff I know I don’t know → architect needs to know this section (broadly)
        - Stuff I don’t know I don’t know → the largest part
    - A large part of the value of an architect is a brad understanding of technology
        - and how to use it to solve particular problems
- Analyzing Trade-offs
    - Thinking like an architect is all about seeing trade-offs in every solution
    - and analyze those trade-offs to determine what is the best solution
- Understanding Business Drivers
    - understanding the business drivers leads to know the requirements for the success of the system

## CH03. Modularity

- There is little that said about how to achieve modularity
- and, it’s hard to define
- Modularity is an organizing principle
    - usually describes a logical grouping of related code
- Architects must be aware of how developers package things
    
    (because, tightly coupled packages are difficult to be reused)
    
- Measuring Modularity
    - Cohesion → what extent the parts of a module should be contained within the same module
        - Cohesive module is one where all the parts should be packaged together
            
            (breaking them into smaller would require coupling)
            
        - Functional cohesion
            
            : Every part of the module is related to the other
            
        - Sequential cohesion
            
            : Tow modules interact, where outputs of one becomes the input for the other
            
        - Communicational cohesion
            
            : Two modules form a communication chain
            
        - Procedural cohesion
            
            : Two modules must execute code inn a particular order
            
        - Temporal cohesion
            
            : Modules are related based on timing dependencies (e.g. startup)
            
    - Coupling 
    → Can be measured with Abstractness, Instability, Distance from the Main Sequence
    - Connascence
    → Two components are connascent if a change in one would require the other to be modified in order to maintain the overall correctness of the system
        - Static connascence: source-code-level coupling
        - Dynamic connascnece: run-time-level coupling

## CH04. Architecture Characteristics Defined

- Architecture characteristic meets three criteria
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/e44a26ef-329f-4598-9429-3f4078727a56/787ced00-094e-4d4e-84d6-4bc24c2320ee/Untitled.png)
    
    - Specifies a non-domain design consideration
    - Influences some structural aspect of the design
    - Is critical or important to application success
- Operational Architecture Characteristics
    
    → cover capabilities
    
    - Availability, Continuity, Performance, Recoverability, Reliability, Robustness, Scalability, …
- Structural Architecture Characteristics
    
    → code structure
    
    - Configurability, Extensibility, Installability, Leverageability, Localization, Maintainability, Supportability, Upgradeability, …
- Cross-Cutting Architecture Characteristics
    - Accessibility, Archivability, Authentication, Authorization, Legal, Privacy, Security, Supportability, Usability, …

## CH05. Identifying Architectural Characteristics

- To identify these, it requires to not only understand the domain problem, but also collaborate with the problem domain stakeholders
- Extracting Architecture Characteristics from Domain Concerns
    - it needs to translate from domain concerns to characteristics “properly”
- Extracting Architecture Characteristics from Requirements
- “There are no wrong answers in architecture, only expensive ones”

## CH06. Measuring and Governing Architecture Characteristics

- Difficult points of measuring architecture characteristics
    - They aren’t physics
    - Wildly varying definitions
    - Too composite
- Operational measures → Statistical analysis (better approach)
- Structural measures → Cyclomatic Complexity

## CH07. Scope of Architecture Characteristics

- Need to re-define coupling and connascence (in a larger scale, b/w microservices)
    - Static Connascence
    - Dynamic Connascence
        - Synchronous → Synchronous calls create dynamic connascence
        - Asynchronous
- Architecture Quantum
    - An independently deployable artifact with high functional cohesion and synchronous connascence
- To know architecture characteristics, it is necessary to know the domain as perfect as I can

## CH08. Component-Based Thinking

- Component scope
    - library, layer, service, …
- Partitioning
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/e44a26ef-329f-4598-9429-3f4078727a56/91c3aa1f-2039-4d3a-b263-2e6f3fc5fe39/Untitled.png)
    
    - Technical partitioning → partitioned by technology (layer)
        - suits well w/ MVC pattern
        - higher degree of global coupling
    - Domain partitioning → partitioned by interest
        - modeled more closely toward how the business functions rather than an implementation detail
        - generates a lot of boiler-plate codes

## CH09. Foundations

- Fundamental Patterns
    - Big Ball of Mud
        - absence of any discernible architecture structure
        - lack of structure makes change increasingly difficult
    - Unitary Architecture
    - Client/Server
- Monolithic vs Distributed Architectures
    - Monolithic → Layered, Pipeline, Microkernel
    - Distributed → Service-based, Event-driven, Space-based, Service-oriented, MSA
    - Distributed architecture styles have significant trade-offs to get more powerful performance than monolithic
        - Fallacy #1: The network is reliable
            - networks still remain generally unreliable
        - Fallacy #2: Latency is zero
            - latency is always greater than local in-memory method call
        - Fallacy #3: Bandwidth is infinite
        - Fallacy #4: The network is secure
        - Fallacy #5: The topology never changes
            - network topology can be easily changed
        - Fallacy #6: There is only one administrator
        - Fallacy #7: Transport cost is zero
            - the cost of maintaining distributed architecture
        - Fallacy #8: The network is homogeneous
    - Distributed transactions → can be managed by transactional sagas

## CH10. Layered Architecture Style

- a.k.a n-tiered architecture → the most common architecture style
- Topology
    - usually four standard layers; presentation, business, persistence, and database
    - business layer and persistence layer are sometimes combined into a single business layer
- Separation of concerns concept makes it easy to build effective roles and responsibility models
- Trade-off of this benefit is a lack of overall agility
- Layers of Isolation
    - Each layer can be either closed or open
        - Closed: request cannot skip any layers
        - Open: can skip
    - Which one is better?
        - Layers of Isolation → changes made in one layer don’t impact components in other layers
        - To support this concept, each layer should be closed
- Adding Layers
    - When we want to protect some components from other layers, then we can add a new layer and protect them
        
        ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/e44a26ef-329f-4598-9429-3f4078727a56/09d07526-d725-4eda-9e9c-5548e3cb66e3/Untitled.png)
        
- Other considerations
    - Layered architecture is a good starting point when it is not know yet exactly which architecture style will be used

## CH14. Event-Driven Architecture Style

- Popular distributed asynchronous architecture style
- Is made up of decoupled event processing components
- Two primary topologies
    - Broker Topology → high degree of responsiveness and dynamic control over the processing of an event
    - Mediator Topology → control over the workflow of an event process
- Broker Topology
    - There is no central event mediator
    - use a lightweight message broker (e.g. RabbitMQ, …)
    - Four primary architecture components
        - Initiating event
            - Initial event that starts the entire event flow
        - Event broker
            - Initiating event is sent to an event channel in the event broker
        - Event processor
            - Accepts the initiating event from the event broker and begins the processing of the event
        - Processing event
            - Asynchronously advertises what it did
    - There is no control over the overall workflow associated w/ the initiating event
        - No one knows when the business transaction ends
        - No one is aware of a crash if a failure occurs
- Mediator Topology
    - Addresses some of the shortcomings of the broker topology
    - Five primary architecture components
        - Initiating event
            - Starts the whole eventing process
        - Event queue
            - Initiating event is sent to an initiating event queue
        - Event mediator
            - Only knows the steps involved in processing the event
            - Generates corresponding processing events that are sent to dedicated event channels
        - Event channels
        - Event processors
            - Listen to the dedicated event channels, and process the event
            - Respond back to the mediator that it has completed its work
    - There are multiple mediators, usually associated w/ a particular domain
        - We can use Spring Integration
    - Mediator can maintain event state and manage error handling, recoverability and restart capabilities
    - Not as highly decoupled as with the broker topology
- Asynchronous Capabilities
    - A unique characteristic over other architecture styles is that it relies solely on asynchronous communication
    - The main issue w/ asynchronous communications is error handling