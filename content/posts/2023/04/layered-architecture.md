---
title: "Layered Architecture"
date: 2023-04-24T16:29:26-07:00
categories: [system-design, software, architecture]
---

Layered Architecture is a monolithic architecture style, which means a single deployment unit for all the code. This style is also known as the *n-tiered* architecture.

<!--more-->



> This is the second post in a series of posts about software architectures. The previous post is [Evaluating Software Architecture Styles](https://umairsaeed.com/evaluating-software-architecture/).
>


Layered architecture is one of the most common architectural styles. It is used in many kinds of applications, spanning the gamut from desktop to web to mobile. Even within other distributed architecture styles, layered is typically used to build out the individual components, modules, and services.

## Overview
This architecture is characterized by logically grouping code into horizontal layers (hence the name). Each layer performs a specific function. The number of layers is flexible, but four primary layers are utilized. These are:

- Presentation layer
- Business Layer
- Persistence layer
- Database layer


{{< figure src="/img/Paper-2023-Architecture-02-Layered-01w.png" width=300 caption="Common layers in Layered Architecture" >}}

In terms of deployment, all the layers are commonly part of a single deployment unit. The layers can sometimes be deployed as separate units, as shown below.

{{< figure src="/img/Paper-2023-Architecture-02-Layered-02w.png" width=100% caption="">}}

## Analysis of Quality attributes

### Deployability (Low)
This architecture has a low score on deployability due to its monolithic nature. The entire application or website must be deployed as one unit, making frequent deployments hard. Even the simplest change requires a complete build and deployment of the whole application.

### Fault Tolerance (Low)
Given the monolithic nature, a bug (e.g., out-of-memory exception) in any part of the code risks bringing down the entire application, leading to low fault tolerance for this architecture.

### Scalability (Low)
Once this architecture has been deployed, little can be done to adjust the load and increase capacity dynamically. The scalability considerations are baked in once the application has been built and deployed.

Dealing with increasing load typically requires updating the code and redeploying. Even then, given the monolithic nature of this architecture, the scalability cannot grow beyond a certain threshold.

### Elasticity (Low)
Similar to scalability, this architecture style scores low for elasticity, as it cannot respond to increased user spikes beyond its baked-in capacity.

### Reliability (Medium)
Layered architecture is the middle of the road for reliability. Given its monolithic nature, once deployed/installed successfully, it is likely to avoid problems with external dependencies (e.g., databases), which helps with reliability.

On the other hand, it has low fault tolerance and a high time to recover (due to low deployability), which hurts the reliability score.

### Performance (Medium)
Layered architecture avoids some of the costs associated with distributed architecture. However, the lack of parallel processing, closed layering, and the sinkhole architecture anti-pattern lock in its overall performance.

### Modularity (Low)
The modularity is low modularity as each layer is built and deployed as part of the monolith. The layers are not standalone *modules* and are not reusable.

### Extensibility (Low)
The extensibility score is low due to low modularity and deployability. New functionality has to be added by changing and redeploying the entire application.

### Testability (Low)
Given the monolithic nature of this architecture, even a simple change often requires running the entire test suite before deployment, which is time consuming and lowers the testability of this architecture.

### Simplicity (High)
Simplicity is one of the strengths of this architecture. This architecture is easy to understand, and the concept of layering code is familiar to all software engineers. Often, each layer grows organically due to code organization best practices. Finally, due to its monolithic nature, this architecture does not have to deal with the complexities associated with distributed architectures.

### Overall Cost (High)
Cost is another advantage of this architectural style. Its simplicity, low barrier to entry, and familiarity make it relatively low-cost to build and maintain.


## Conclusion
Layered architecture is a good choice for small and straightforward applications or websites. It is also well-suited for small teams. Its simplicity makes it ideal for building prototypes and bootstrapping new products. Once the size of the application grows, teams should move away from this architecture, as the low deployability and its monolithic nature become an impediment to agility and shipping features. Moreover, it is hard to scale this beyond a certain number of users.


