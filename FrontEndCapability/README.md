# Enterprise Architecture Principles (Front End Capability).

The purpose of this section is to answer our architectual 

____

## Think Integrity: Guard the accuracy of your data 
Data integrity is broken down into two modes of thought; *process* and *state*. As far as process is concerned the front end should put as mirror steps in place to final quality gates to ensure that the data that is transmitted gets as close to the desired standards as possible. When it comes to state management, make it transactional, and only surface state that to top-level scope that is business or domain relevant.

## Think consumer-grade: Use design thinking 
Consistant frameworks for design over implentation solutions.


## Think ecosystems: Build for long term efficiency and maximum value


## Think Security: Design for real world threats and to maintain compliance


## Think cloud: Build for scale and agility


## Think real-time: Integrate immediate analysis with transactions, events and context
What where and when of actionable business events that occur should handle in a clearly defined front-end transaction. Keep event hooks in mind as architecting these solutions, for potential broadcasting and logging.



## Think digital business: Use event-driven architecture in the event-driven world

 
## Think business objectives: Choose macroservices, miniservices or microservices
This particular architectural guideline has a tangential effect on the frontend. When creating front-end applications favor domain driven microservices first, then explore aggregation APIs second. Only when the performance of microservices begins to degrade the client application, should you try to create APIs to format data specific to client-side representation. The front end making decisions about data manipulation this would be a *red flag.*


## Think business outcomes: Organize around capabilities not technologies


## Think reusability: Develop solutions that maximize reuse and control technical diversity
Don't repeat yourself (DRY).  Extract similar functionalities into a common place. This will help minimize bugs, and make long term maintenance easier. See [Atomic Design](implementation/atomic-design.md) for a more in depth look at how this can be accomplished with components and Vue.


## Think insight: Design for analytics and intelligence everywhere


## Think Innovation: Focus on differentiating over common use cases

