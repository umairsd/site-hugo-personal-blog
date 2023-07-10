---
title: "Service Oriented Architecture"
date: 2023-05-24T08:17:56-07:00
categories: [system-design, software, architecture]
---

Service-Oriented Architecture (SOA), also known as, Service-Based Architecture (SBA), is a distributed architecture pattern in which an application is organized into a collection of loosely coupled, reusable **services**. Each service is a self-contained unit that performs a specific task or function, and communicates with other services using standard communication protocols, such as REST, SOAP, or gRPC.

<!--more-->


> This is the fourth post in a series of posts about software architectures. The previous posts are:
>
> - [Evaluating Software Architecture](https://umairsaeed.com/evaluating-software-architecture/)
> - [Layered Architecture](https://umairsaeed.com/layered-architecture/)
> - [Pipeline Architecture](https://umairsaeed.com/pipeline-architecture/)
>



## Overview

This architecture has the following key components:

1. Services: The self-contained units that provide specific workflows and functionalities. One crucial aspect of service-based architecture is that it typically uses a shared database.
2. User interface: Clients or components (could be other services) that use these services.
3. Communication protocol: The standardized method for exchanging data and invoking services, such as REST, SOAP, or gRPC.

The basic topology follows a layered structure with a separately deployed user interface, separately deployed course-grained services, and a monolithic database.

The example below contains four different services, each deployed separately.

{{< figure src="/img/Paper-2023-Architecture-04-SOA-01w.png" width=90% >}}

A best practice for this architecture is to add an API layer between the user interface and the services. The API layer consists of a reverse proxy or gateway and consolidates shared functionality (such as aggregating data from multiple services, metrics, service discovery, etc.).

{{< figure src="/img/Paper-2023-Architecture-04-SOA-02w.png" width=90% >}}

Many topology variants exist within the service-based architecture style, making it very flexible. For example, the example below shows a system with the user interface broken up into two deployable units, `X` and `Y,` and two databases, `E` and `F`. The database `E` is shared between services `A`, `B`, and `C`.


{{< figure src="/img/Paper-2023-Architecture-04-SOA-03w.png" width=90% >}}


## Quality attributes scores

### Deployability (High)
Service-based architecture has a high deployability score. Each service is a separately deployable unit. In addition, it is possible to split the user interface and databases into multiple deployable units.

A large number of small deployment units enables frequent deployments and reduces the risk of a single deployment, both of which lead to a high deployability score.

### Fault Tolerance (High)
The decoupled nature of services in SOA allows for the isolation of failures, ensuring that a fault in one service does not necessarily lead to a complete system failure.

The overall fault tolerance is lower than the microservices architecture because the services in SOA tend to be coarse-grained.

### Scalability (Medium)
Service-based architecture scores a medium on scalability due to two factors. (1) The coarse-grained nature of the services, and (2) the shared database.

Both of these can limit scalability under heavy loads.

### Elasticity (Medium)
Similar to scalability, the coarse-grained services and the shared database limit the elasticity of this architecture.

### Reliability (High)
The isolation and independent deployment of services contribute to the overall reliability of the system, as it reduces the impact of failures and allows for easier recovery.

The coarse-grained nature of the services benefits this attribute. Fewer services mean lesser network traffic to and between services, fewer distributed transactions, and less bandwidth usage, increasing the system's overall reliability.

### Performance (Medium)
The overhead of inter-service communication and potential latency introduced by network calls between services negatively impacts the performance of this architecture style.

On the other hand, the distributed nature of the services presents opportunities for parallel processing, leading to a medium overall score.

### Modularity (High)
This architecture style promotes the separation of concerns, making it easier to develop, maintain, and scale individual services independently, leading to a high score on modularity.

### Extensibility (Medium)
The service-based architecture gets a medium on extensibility. On the plus side, new functionality is added by building new services without impacting existing services. On the negative side, the services are coarse-grained, which means often adding new functionality requires modifying and redeploying existing services which hurts the score.

### Testability (High)
This architecture scores high on testability as high modularity limits the scope of the domain for a given service, and high deployability means that frequent deployments are less risky.

### Simplicity (Medium)
As a distributed architecture, service-based architecture is more complex than monolithic architecture. However, this is one of the most straightforward distributed architectures, leading to a medium overall rating.

### Overall Cost (High)
All distributed architectures have higher costs than monolithic architectures due to higher complexity and more resource usage. Specifically, distributed architectures increase infrastructure and operational costs due to the need for managing and maintaining multiple services, load balancing, and monitoring.

## Conclusion
Service-based architecture is a hybrid of the microservices architecture style and is considered one of the most pragmatic architecture styles, primarily due to its architectural flexibility.

It has the advantages of a distributed architecture yet avoids some of the complexity and costs of other distributed architectures, such as microservices or event-based architectures.

Service-based architecture is a popular choice for many teams and applications. I highly recommend this architecture style for early stage companies and startups — due to its good all-around scores for quality attributes. Moreover, given its flexibility, the architecture can evolve relatively easily as the product grows.
