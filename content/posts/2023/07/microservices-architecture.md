---
title: "Microservices Architecture"
date: 2023-07-06T09:47:05-07:00
author: Umair Saeed
categories: [system-design, software, architecture]
---

Microservices Architecture is a distributed architecture in which an application is organized into a collection of small, loosely coupled, and independently deployable services. Each microservice is responsible for a specific piece of functionality, operates within its process, and communicates with other microservices using lightweight protocols such as REST, gRPC, or messaging systems.

<!--more-->

> This is the seventh post in a series of posts about software architectures. The previous posts are:
>
> - [Evaluating Software Architecture](https://umairsaeed.com/evaluating-software-architecture/)
> - [Layered Architecture](https://umairsaeed.com/layered-architecture/)
> - [Pipeline Architecture](https://umairsaeed.com/pipeline-architecture/)
> - [Service Oriented Architecture](https://umairsaeed.com/service-oriented-architecture/)
> - [Event Driven Architecture](https://umairsaeed.com/event-driven-architecture/)
> - [Space Based Architecture](https://umairsaeed.com/space-based-architecture/)
>


## Overview
The core philosophy of microservices is that each service should completely model a domain or workflow and includes everything necessary to accomplish its tasks, including other subcomponents and databases.

Microservices have the following four key components:

1. (Micro) services: Small, focused services that provide specific functionality, usually exposed through well-defined interfaces.

2. API Gateway: A single entry point for external clients, which routes requests to the appropriate microservices and handles cross-cutting concerns like authentication and rate limiting. Often, this includes service registry and discovery.

3. Communication protocol: The standardized method for exchanging data and invoking services, such as REST, gRPC, or messaging systems[^1].

4. Data storage: Ideally, each microservice has its own storage system based on the specific needs of the service.


{{< figure src="/img/Paper-2023-Architecture-07-Microservices-01w.png" width=90% >}}

[^1]: e.g. RabbitMQ or Kafka



### Comparison with Service-Oriented Architecture
Service-oriented architecture (SOA) and microservices architecture are both architectural patterns that structure an application as a collection of services. However, there are some critical differences between the two:

1. **Service granularity**: Microservices are typically smaller in scope and focused on a single, specific functionality. Services in SOA are typically more coarse-grained and encompass a broader range of functionalities.

2. **Service communication**: SOA often relies on standardized protocols and message formats like SOAP, WSDL, and XML for communication between services. In contrast, microservices use lightweight communication mechanisms such as RESTful APIs, JSON, or gRPC.

3. **Service independence**: Microservices are self-contained, with their own data storage and minimal dependencies on other services. In SOA, services may share data storage and have more dependencies on other services, potentially leading to a higher degree of coupling.

4. **Deployment**: Microservices are designed for independent deployment, allowing continuous delivery and deployment of individual components without impacting the entire system. SOA does not emphasize independent deployment, and updates to a service may require cascading updates to other services.


## Analysis of Quality attributes

### Deployability (High)
This architecture has a high deployability rating as microservices enable easy and independent deployment of individual services, enabling faster updates without impacting the entire system.

Microservices couldn't exist without the DevOps revolution and the relentless march toward automating operational concerns (automated deployment, testability, etc.)

### Fault Tolerance (High)
The decoupled nature of microservices allows for the isolation of failures, ensuring that a fault in one service does not necessarily lead to a complete system failure, leading to a high score for fault tolerance.

### Scalability (High)
Scalability is one of the core strengths of this architecture, making this one of the most scalable architectures.

Microservices architecture supports both horizontal and vertical scalability. The granular services allow individual services to be scaled independently based on their resource requirements and workload.

### Elasticity (High)
Similar to scalability, this architecture scores well for elasticity. The ability to horizontally scale individual services grants the system a high degree of elasticity.

### Reliability (High)
As the services are independent & single-purpose, a failure in one service does not automatically lead to the collapse of the entire system. Moreover, horizontally scaling each service enable redundancy, leading to even higher reliability. The high deployability score also helps, as it reduces the impact of failures and allows for easier recovery.

### Performance (Low)
The performance of this architecture suffers due to the high degree of inter-service communication, which increases the latency of a request.

### Modularity (High)
The microservice architecture is highly modular by definition. The system is built up of small, independently developed, and maintained services, leading to a high modularity score.

### Extensibility (High)
As the services are granular and independent, adding new functionality becomes easy. Often, new workflows can be added by simply combining existing services. Updating an existing service limits the scope of the change, making it easy to test and deploy.

### Testability (High)
Microservices can be tested independently, allowing for more focused and efficient testing of individual components.

### Simplicity (Low)
While microservices can simplify the design of individual services, the overall architecture is highly complex due to inter-service communication, data consistency, and coordination.

### Overall Cost (Low)
Microservices can reduce development and maintenance costs by enabling modularity and extensibility. However, they may increase infrastructure and operational costs due to the need for managing and maintaining multiple services, load balancing, and monitoring.

## Conclusion

Microservices architecture offers numerous benefits, including increased modularity, independent deployment, and scalability, making it an excellent choice for cloud-native applications, e-commerce platforms, and large-scale enterprise systems. For example, in an e-commerce platform, microservices can efficiently handle diverse functionalities, such as inventory management, user authentication, and payment processing, by decomposing the system into small, independent components. This architecture allows for rapid development, streamlined maintenance, and the ability to scale individual services based on specific needs, ultimately resulting in a more resilient and adaptable system that can better meet the demands of a competitive market.
