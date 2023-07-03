---
title: "Event Driven Architecture"
date: 2023-07-03T09:45:12-07:00
categories: [system-design, software]
---


Event-Driven Architecture (EDA) *uses events to trigger and communicate between decoupled nodes (services)* within the system. Producers generate initiating events (messages) when a significant change in state occurs. Components within the system subscribe to events and react to them by performing specific actions or tasks.

<!--more-->

> This is the fifth post in a series of posts about software architectures. The previous posts are:
>
> - [Evaluating Software Architecture](https://umairsaeed.com/evaluating-software-architecture/)
> - [Layered Architecture](https://umairsaeed.com/layered-architecture/)
> - [Pipeline Architecture](https://umairsaeed.com/pipeline-architecture/)
> - [Service Oriented Architecture](https://umairsaeed.com/service-oriented-architecture/)
>


## Overview
This is an asynchronous distributed architecture, often used to produce highly scalable and high-performance applications. There are two primary topologies of Event-driven architecture.

### Broker Topology
In this topology, all events are sent to a centralized broker, which then forwards them to the relevant consumers (event processors). The broker acts as a central hub for event communication.

In the example below,
- The initiating event (1) is sent to the event channel in the event broker for processing.
- As there are no mediators, a single event processor (`A`) accepts the initiating event and begins processing.
- Once the event is processed, processor `A`  advertises to the system via a *processing event*, (2). This processing event is sent asynchronously to the broker.
- Other interested processors `B`, `C`, and `D` listen to the processing event, process it, and optionally generate a new processing event (3) to advertise to the system what they did.
- Processor `E` listens for this event and handles it.

{{< figure src="/img/Paper-2023-Architecture-05-EventDriven-01w.png" width=90% >}}

### Mediator Topology
The event mediator is core to this event-driven topology, which manages and controls the workflow for initiating events. A key difference from the broker topology is that the event mediator knows the steps involved in processing the event and generates corresponding processing events in a point-to-point messaging fashion.


{{< figure src="/img/Paper-2023-Architecture-05-EventDriven-02w.png" width=90% >}}


In most implementations of the mediator topology, multiple mediators focus on a particular domain, alleviating the single point-of-failure issues associated with Event-Driven systems and increasing overall throughput and performance.

In the example below, there are three separate event mediators.
- Event mediator `MA` *processes* the initiating event and sends it to the event channel for processor processor `A`.
	- Event processor `A` listens to this channel, processes the event, and responds to the mediator to indicate completion.
- Event mediator `MA` also forwards the initiating event to the event queues for mediators `MB` and `MC`.
- Mediator `MB` processes the event and sends a *processing event* to the event channels of processors `B` and `C`. These processors listen to their respective channels and process the incoming events.
- Similarly, mediator `MC` processes its event and sends a *processing event* to the event channel of processor `D`.


{{< figure src="/img/Paper-2023-Architecture-05-EventDriven-03w.png" width=90% >}}


### Comparison
- Broker topology does not have a centralized event coordinator. Various processors sign up to be interested in specific events leading to a more decoupled system. On the other hand, mediators are aware of all the producers and consumers in the system, often via point-to-point connections leading to tighter coupling within the overall design.

- Event processors in broker topology advertise what they did to the rest of the system. The communication in mediator topology is typically point-to-point.

- The mediator topology can handle complex chains of event processing logic.


## Analysis of Quality attributes

### Deployability (Medium)
The event-driven architecture enables the decoupling of components allowing for easier deployment and updates to individual components without affecting the entire system.

### Fault Tolerance (High)
EDA's decoupled nature makes it possible to build fault-tolerant systems, as the failure of one component may not necessarily lead to a complete system failure. Additionally, the use of message brokers can help ensure message delivery even in case of temporary component failure.

### Scalability (High)
Scalability is another strength of the event-driven architecture. High scalability is realized through horizontal scaling by programmatically adding event processors.

### Elasticity (High)
Similar to scalability, event-driven systems can adapt to changes in workload by dynamically scaling components up or down based on the number of events in the queue.

### Reliability (Medium)
By using message brokers, EDA ensures the reliable delivery of messages and can even implement retries in case of failures, contributing to the system's overall reliability. However, due to its asynchronous nature, data loss can become an issue that negatively impacts the overall reliability of this architecture.

### Performance (High)
Asynchronous communications combined with highly parallel processing leads to a high score for performance.

### Modularity (High)
EDA encourages modularity by decoupling components, allowing them to evolve independently.

### Extensibility (High)
Adding new features through existing or new event processors is relatively straightforward, particularly in the broker topology. The architecture makes it easy to add, modify or remove components without a major impact on the overall system, leading to a high rating for extensibility.

### Testability (Medium)
Testing individual components can be straightforward, but testing interactions between components (e.g., event flow) can be more challenging due to the asynchronous and nondeterministic event flows.

### Simplicity (Low)
EDA can simplify the overall design of a system by decoupling components. However, the asynchronous and distributed nature of the architecture can introduce complexity in understanding event flows and handling failures.

### Overall Cost (Medium)
High modularity and extensibility help with the overall maintenance costs of this architecture. However, the need for message brokers and managing distributed components increases operational and infrastructure costs.


## Conclusion

Due to its adaptability, it can be used both as a standalone architecture style or embedded within other architecture styles (e.g., event-driven microservices). Event-driven architecture (EDA) is well-suited for applications requiring real-time responsiveness, scalability, and adaptability, such as real-time analytics, IoT systems, and complex event processing. EDA excels in these scenarios by leveraging loose coupling, enhanced scalability, and real-time event processing, making it an ideal choice for dynamic, data-intensive applications.

