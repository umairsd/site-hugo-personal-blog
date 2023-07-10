---
title: "Software Architecture - It All Depends"
date: 2023-07-10T09:13:13-07:00
draft: true
categories: [system-design, software, architecture]
---

Now that I've reviewed some of the key software architectural styles, let's see a side-by-side comparison of various architectures in terms of the architectural quality attributes:

<!--more-->

{{< figure src="/img/Paper-2023-Architecture-08-ArchitectureStylesSummary.png" width=90% >}}

This table shows that choosing the right architecture involves tradeoffs. Different architectures have their strengths and weaknesses, and thus the "best" architecture style is highly dependent on the context (goals, business needs, budget, etc.).

Another observation is that the service-oriented architecture scores medium or high across all the attributes, making it a good middle-of-the-road architecture. It can be a good choice for web-based applications.

When making an architectural choice, a good insight to keep in mind is that the current choice should be good for the current needs of the business. Over time, the business *will* evolve, and as a result the software architecture will need to evolve over time.

Finally, I've converted the previous blog posts into a downloadable [PDF sheet](/data/ArchitectureStylesComparison.pdf) that you can download.


The full list of all the posts in this series is:
- [Evaluating Software Architecture](https://umairsaeed.com/evaluating-software-architecture/)
- [Layered Architecture](https://umairsaeed.com/layered-architecture/)
- [Pipeline Architecture](https://umairsaeed.com/pipeline-architecture/)
- [Service Oriented Architecture](https://umairsaeed.com/service-oriented-architecture/)
- [Event Driven Architecture](https://umairsaeed.com/event-driven-architecture/)
- [Space Based Architecture](https://umairsaeed.com/space-based-architecture/)
- [Microservices Architecture](https://umairsaeed.com/microservices-architecture/)
- [Software Architecture: It all depends](https://umairsaeed.com/software-architecture-it-depends/) (this post)



