# Enterprise Architecture Principles (Front End Capability).

The perpose of this section is to answer our architecutal 

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
By focusing on capabilities and not technologies, we are able to identify our end goal, and build our infrastructure, teams and technology stacks to best achieve that goal while taking future initiatives into account during our planning. By focusing on technology first, we run the risk of building a system that can become quickly outdated, and siloed, maintainable only by specialized developers in that technology.

CHG examples of capability-driven projects

PDE: 
* The capability was first defined (online portal to help our providers better interact with us)
* Team created to decide the technologies we would use to achieve our goal
* Cross-functional (full-stack) team created to build out this capability

Front-end Sites
* Capability defined – move our sites from the existing platform onto a new platform
* Team created to decide the technologies we would use to achieve our goal
* Cross-functional (full-stack) team created to build out this capability – each site built on the same foundation, but using different technologies as needed to achieve it’s goals (Job Board and Lead submission functionaly)

CHG example of a technology-driven project

SharePoint public sites
* Someone can correct me if I’m wrong, but the reason we went with Sharepoint was because we had the license already, so let’s use it.
* A highly specialized team of SharePoint developers was created to build and maintain these sites, who to this day (5 or 6 years later?) are the only ones that can effectively access and maintain these sites (only 1 site left!)
* Band aid Fixes had to be developed along the way to deal with exapnding technology needs - build a tempoary "shim" to submit leads to the new leads API




## Think reusability: Develop solutions that maximize reuse and control technical diversity


## Think insight: Design for analytics and intelligence everywhere


## Think Innovation: Focus on differentiating over common use cases

