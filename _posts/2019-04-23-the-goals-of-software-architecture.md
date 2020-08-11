---
layout: post
title: The goals of Software Architecture
gh-repo: patrykwozinski/patrykwozinski.github.io
gh-badge: [follow]
tags: [software architecture, architecture]
comments: true
---

## The purpose of software architecture
Hello! Nice to see you reading my next short article on Medium. This time I will discuss the subject of software architecture and more precisely the goals that accompany it.

> “Whatever it is, I see it as a network of microservices! Necessarily with Kafka and MongoDB! “- Sebastian, senior developer at X .

Ok then, we can create our new functionalities in the form of microservices, actually — we can do the same with old ones that are a nuisance because keeping them costs one hundred times more than writing new lines of code. Let us now ask ourselves one incredibly important question: what is the purpose of implementing our new architecture?

## What is the goal of architecture in the context of our system?
When making design decisions, we face very serious problems that are related to the selection of architectural solutions. Their choice should be primarily based on the goal — that is something that we are able to obtain by implementing a new solution.

We cannot come up with ways to solve problems when we do not know yet what our goal is and how we can achieve it.
- **Scalable architecture** — this is a goal that we can set ourselves by observing that current solutions are not effectively doing their job, that they need a lot of scaling, which is not possible. Often the very approach to infrastructure or implementation architecture is of great importance here. The appropriate construction of specific parts of the system based on small, independent services will enable us to scale horizontally.
- **Architecture easy to understand** — this is what we choose when we know that a specific project will not solve our core functionalities, but will become something that we would like to quickly go through and later be able to easily show to new developers who could solve tasks after a short introduction.
- **Testable architecture** — an architecture that allows you to check each element using individual / acceptance / functional tests. Adding tests should be quick and easy, which is why we are sure of the stability of such a solution.
- **Stable architecture** — aka. 100% uptime, so the dream of every SysAdmin. This probably doesn’t need any explanation; for example, we aim to create a system that is resistant to any external factors. An interesting fact about building and designing such architecture is certainly Chaos Monkey by Netflix.
- **Removable architecture** — yes, I mean solutions that we will not miss after throwing them into the rubbish bin. Such architecture allows for the very easy exchange of its elements like building blocks, as well as removing individual modules in a safe manner, which does not involve greater consequences like destroying half of the system. We simply take a chisel and pick out specific parts of the application one by one.
- **Measurable architecture** — concerns solutions that we can easily observe and in which we can react to any anomalies. A very nice goal when we want to create a system that will be developed for many years — thanks to the appropriate metrics we will be able to check whether development is going in the correct direction.
- **Evolutionary architecture** — this goal is also made from other items listed in this entry. But briefly: we want our system to evolve along with business, it is especially important when we create an application for a startup which is constantly looking for its “inner self” and the best way to solve the problem posed to it. Our task is to enable developers to quickly and conveniently expand the software without falling into blockers, which inhibit most of the developing systems.
- **Easy to monitor architecture** — monitoring-driven-architecture, a popular concept in recent times. Proper tracking of the system’s behavior gives us a great insight into the upcoming problems, and thanks to that we will be able to react to anything once something disturbing appears on our colorful dashboard.
- **Hype-driven architecture** — the goal we aim at as soon as we get a new sneak peek at a new language, a new framework and other things. Super, wow — we have GraphQL coupled with the latest solutions in Scala, which we conclude using the most spectacular database! Such a goal is often ridiculous — at least — but its advantage is that someone will be able to satisfy their ego and try new things that will later be discussed at the next conference for people with black backpacks. ;) It is a pity that often such solutions based on new technologies and new approaches are difficult to maintain. Later on, it will turn out that you will feel the looks of random people in the park that have inherited this code from you and have found out how you look.


## The goal is priority!
There are still many more goals, and in fact, only the collection of those that correspond to our project and its needs can help us with the selection of specific solutions and in making decisions related to the implementation architecture and application or system architecture. Yes, there are many levels of architecture, but I will write a separate entry about it. Buzzword for today: C4Model.

In addition, the collected tasks that our solution is to implement are a great basis for explaining to our superiors and teammates why the application needs changes and / or what we should focus on when designing new modules, services and more.
