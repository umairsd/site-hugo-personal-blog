---
title: "Evaluating Software Architecture"
date: 2023-04-17T11:28:21-07:00
categories: [system-design, software]
---

Software architecture is very context-dependent. Factors such as the software application's goals, the development team's size and budget, and business needs play a significant role in the choice of software architecture.

<!--more-->

The architecture goals for an early-stage company (i.e., focused on quick prototyping to find product-market fit) are very different from those of a  growth-stage company (rapid growth in the number of users) or a company with a stable base of customers who expect the product to "just work."

All architectures come with their strengths and weaknesses, so how best to evaluate various decisions and tradeoffs when analyzing architectural choices? Architectural quality attributes (*aka* non-functional requirements) are the criteria used to evaluate software architectures and compare them systematically.

## Architectural Attributes

There is a large number of [quality attributes](https://en.wikipedia.org/wiki/List_of_system_quality_attributes) used to evaluate a system. However, I am going to use the following 11 attributes as I find them the most compelling for evaluating various architectural choices.

### Deployability
Deployability measures the ease with which software can be allocated to "an environment for execution." Moreover, it measures the ease with which the engineering or dev-ops teams can roll back the new deployment in case of issues. Ease means a predictable and acceptable amount of time and effort.

The frequency of deployments, percentage of successful to failed deployments, time per deployment, and the number of issues per deployment are some of the metrics used to measure the deployability attribute.

### Fault Tolerance
Fault tolerance is the software's ability to handle error and boundary conditions and the time to recovery in failures (e.g., power outage, hardware failure, etc.)

### Scalability
Scalability is an application's ability to handle a large number of concurrent users without significant performance degradation.

### Elasticity
Elasticity is an application's ability to handle a sudden spike of requests. Usually, this attribute correlates with scalability. If an application is architected such that it can scale well, it will likely be able to handle a burst of users as well.

### Reliability
The reliability attribute measures the degree to which a software system performs specific functions under certain conditions. A few factors contribute to this, such as:

- Is the software available when the users need to use it?
- Does the software do what it is supposed to? How buggy is it?
- How well does the software handle external failures, such as hardware or software issues? This factor has some overlap with fault tolerance.

### Performance
Performance is a measure of the responsiveness of the system. How long does it take to complete a task (e.g., a user request)? Typically, this attribute includes both the latency (time spent on responding to an event) and capacity (the number of events handled in a given time).

### Modularity
A software module is a logical grouping of related code. In software architecture, a module is a general term to denote a related collection of code that is independently deployable, reusable, and provides a concise and standardized interface (API) to its users.

Modularity measures how well the overall system is built out of modules.

### Extensibility
The extensibility attribute measures how easy it is to change the functionality of a software application. Shifts in business priorities, customer demands, and new technologies often necessitate changing a software application.

### Testability
Testability measures how easy it is to test an application to find bugs and to ensure that it meets all the criteria (i.e., "Does the software work? How well?"). One way to measure testability is through code and path coverage tools.

### Simplicity
Simplicity measures how easy it is to understand and build a software system. It is *somewhat* subjective, as it often depends on the development team's experience, skills, and budget.

### Overall Cost
Finally, at a high level, the cost is the monetary expense of building and maintaining an architecture. It includes the time to build the system, the resources (hardware, licenses, etc.), and the costs of employing a team of engineers.


--

**This is the first post in a new series of blog posts to analyze some common software architectures**. The primary goal is to provide a high-level overview of each architectural style and see how they stack against each other. I will use the set of architectural characteristics defined in the post to analyze each of the architectures in turn.

