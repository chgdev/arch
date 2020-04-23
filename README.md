[![GitHub version](https://badge.fury.io/gh/chgdev%2Farch.svg)](http://badge.fury.io/gh/chgdev%2Farch)

# Enterprise Architecture Principles

The crux of what these principles should be and do are as
follows:

Is it clear? – If it isn’t clear, let’s word smith it or
change it to be clear.  

Does it align? Is this indeed the direction we are trying to go?  

Does it guide good decision making? Can the principle be applied to help make
good decisions? 

____

## Think Integrity: Guard the accuracy of your data 
When building an application we should closely control database and file system access to ensure data integrity and consistency. Create a small number of privileged data services, optimized for efficiency in data access, and ensure these are the only services that are allowed to access the data stores directly the internal design and language are, of course, fully hidden behind the SOA-style, well-documented interface of the service that's accessible via standard means, like REST, SOAP or message-oriented APIs.

We originally defined SSN field in Salesforce as unencrypted.  Later security concerns lead us to want to mask SSN but a few of the architecture decisions made it so it was not possible to do so, so we created a second masked SSN field that synched with the original.  Then additional fields were created for SSN and it has led to copies of a provider’s SSN being in a multitude of places and creating many opportunities for data integrity to be compromised.  

## Think consumer-grade: Use design thinking 
Empathy is the key word here, understanding the usage patterns and needs of the customer, especially when the customer is the business.  Think "mobile first" and with the end consumer in mind.  This doesn't mean that all our front ends have to be or will be mobile. Rather, it implies that the fundamentals of mobile application design and architectures are not just for mobile devices but all platforms and help create a model of design and architecture that should be used in the majority of new application designs.  The majority of web traffic (57%) is viewed on mobile devices and represents the experience most users are comfortable.  
with.

A classic example of design thinking is Oral B and their USB toothbrush.  The challenges customers have is making sure they are brushing enough, charging the brush and ordering replacement heads.  They made a brush that collected metrics like a fitness tracker and when plugged into a USB port would download the data, charge the device and prompt the user to order replacement heads when use levels indicated it was time to replace the brush head.

## Think ecosystems: Build for long term efficiency and maximum value

More value can be derived from broad alignment than from localized excellence.  Nothing acts in a silo and the interaction of
systems or the “ecosystem” of the organization must always be considered.  There are no sacred cows, there are no
systems that operate purely independently. Systems must serve other systems and the greatest value for the business
will come from the highest level of interoperability.      

Specialty is a current challenge.  Specialy exits in a variety of places, and multiple applications are created around specialty.  It has led to a varaity of applications and data marts with no single source of truth. An ecosystem would consider all needs and provide a better source of truth with interoperabiltity of more systems. 

## Think Security: Design for real world threats and to maintain compliance

Understand the actual risk models (impact and likelihood) and clearly communicate the risks and design/build to mitigate in manner that matches the overall risk.  Don’t over complicate when the risk is low and don’t build in controls that do not actually provide protection.  Think about security in layers and how we can leverage a variety of solutions to build a layered security approach.


## Think cloud: Build for scale and agility

We want to be able to push the business and be a growth partner not be a division that slows down the pace of business.  We must avoid architectures that cannot scale with the growth goals of the business or creates such rigidity that we cannot adapt to disruptions with relative ease. Use Separation of Concerns to Foster Agility, Efficiency and Resilience. We should strive to avoid locking together software that may need to evolve independently or be used differently.


## Think real-time: Integrate immediate analysis with transactions, events and context

Digital business strives for intelligence in its decision making — often in real time. The better informed the decision — the better the chance of intelligent and positive outcomes. Most of that data should be well-structured and of dependable quality. Most of the processing of that data is embedded in custom-coded business logic.  Real time architectures use immediate analysis.


## Think digital business: Use event-driven architecture in the event-driven world

Event processing software is decoupled and extensible, it is also stateless and adaptively scalable, while allowing for isolated versioning. It is natively intermediated, tolerates disruptions, and is ready to benefit from in-memory and parallel computing. As such, it is a natural choice for business activity monitoring, interactions with the IoT, multi enterprise integration and extreme transaction volume applications, like Internet gaming and some securities trading systems.

A doctor accepting a job is an event, just like in real life when this event happens it triggers action from other departments, our systems should recognize this change of state and should notify other systems of the event. 

 
## Think business objectives: Choose macroservices, miniservices or microservices

Utilize the chosen decomposition method and then choose the right service for your application. There isn’t a silver bullet that will be your end all be all. We don’t want to overcomplicate something that is very simple and make it difficult to maintain. MVP. 


## Think business outcomes: Organize around capabilities not technologies

We aim to deliver capabilities, technologies are simply resources that help us do so**. ** The business is organized around capabilities as should IT.  We should align our capabilities with the capabilities of the business and focus on business
outcomes.  When we lead with a technical choice, we lose focus of what we are aiming to deliver.   


## Think reusability: Develop solutions that maximize reuse and control technical diversity

As we maximize our reuse, we will be able to focus more on capabilities for the organization. This will in turn allow us to get the business what they need before they even know they need it.

As the teams communicate affectively, we will be able to see where/what we can reuse code and processes. Sharing what has already been done, allows other teams to consume those resources.  Creating a PDF generator for one PXT that can be reused by other PXT’s.


## Think insight: Design for analytics and intelligence everywhere

Some of the best decisions are made when you take the subjective opinions out of the equation and look at how the data on how things are being used and done today. Analytics and intelligence has the ability to tell an unbiased story of what is happening currently. 
Think of the story that needs to be told and ensure the design can support the gathering of information required to tell the story.


## Think Innovation: Focus on differentiating over common use cases

When innovation is in place success is bound to follow. The companies that follow the status quo are the ones that fall behind, but those that are innovate and able to take calculated risks, will grow and flourish in their respective industries.  When we are evaluating use cases and user stories, identify the features that drive innovation.  ** Find the unmet and unarticulated needs.**

Many times we are presented with a business need and it is not a competitive advantage but it is important.  For example nobody would say email is not important, but the value we would get out of building our own is minimal and would be an immense effort to do what is now commodity.  We are better using our resources to develop innovative ideas that better serve our customers.  PDE is an example of an effort that will give us an innovative competitive advantage.  As we review user stories, look for those that provide a competitive advantage and mazimize their value.  When presented with a common use case, try and look to find commodity solutions.
