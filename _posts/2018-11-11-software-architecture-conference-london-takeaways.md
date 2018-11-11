---
layout: post
title: Takeaways from the O'Reilly Software Architecture Conference - London Edition
description: This is what I learnt at the O'Reilly Software Architecture Conference, which took place from 31/10 to 1/11.
---
Last week I attended the London edition of the O’Reilly Software Architecture conference. During the conference I took live 
notes on [twitter](https://www.twitter.com/npcevans). Now the dust has settled it's time to reflect on the main themes. 
Disclaimer: my impressions of the conference are a function of the talks that I decided to attend. 
A different attendee may come to different conclusions.

## The omnipresence of DDD

Concepts from [domain driven design](https://conferences.oreilly.com/software-architecture/sa-eu/public/schedule/detail/68861) were so 
omnipresent they have become (in an interesting meta self-reflecting way) a 
_ubiquitous language_ for talking about software architecture. Many speakers used these concepts to frame their talks. 
The _bounded context_ was especially popular for describing the self-sufficiency required of microservices. And what kind of 
developer would dare embark on a new project without carefully thinking about their domain model, defining their aggregates and 
partitioning off their sub-domains?

## Event-driven architectures

When it comes to modelling domains, a popular approach is to think not only in terms of entities (the things in the domain) but also 
in terms of events (the things that happen). An [event-driven architecture](https://conferences.oreilly.com/software-architecture/sa-eu/public/schedule/detail/70798) 
is one that is structured around the production and consumption of events (rather than on CRUDing entities). _Event storming_ is a 
group modelling exercise for identifying the constituent events in a domain. _Event sourcing_ is an architectural pattern in which
all changes to the state of the application are expressed as events. A particular style of event-driven architecture is _CQRS_ 
(Command Query Responsibility Segregation). In this approach queries are sharply distinguished from commands (or events). 
Commands are persisted in an event store, and the state of an entity is then derived by summing up the relevant commands.

## Kafka as a database

Using microservices to implement an event-driven architecture results in each microservice constructing its own view of the world. 
This view is the sum of the events the microservice has seen and is persisted in its own database. In this setup it is essential that an event 
is never missed, otherwise the microservice’s state will become irredeemably corrupted. Kafka’s persistence and delivery guarantees
resolve this issue. The events stored in Kafka become the single source of truth that determine the state of the 
entities in the domain. This database like quality is further accentuated if one performs stream processing on the events before 
they are consumed by the relevant microservice. This processing can perform joins and aggregates which means the data is 
delivered to the consumer in exactly the right shape. The consumer then only has to store the processed events as they 
are and can answer queries without needing further processing.

## Microservices, microservices, microservices and Kubernetes

Microservices continue to form the basic building blocks of any architecture. When it comes to actually running these microservices, 
Kubernetes has become the platform of choice. That Kubernetes has emerged as the winner of the container platform wars is 
confirmed by the fact that a new project like [Istio](https://conferences.oreilly.com/software-architecture/sa-eu/public/schedule/detail/70784) 
can only be run on Kubernetes. By adding so called _side-car containers_ to a Kubernetes deployment, Istio allows several 
resilience and security patterns to be easily implemented.

## Serverless

And then for those who don’t want to deal with the complexity of building, deploying and maintaining containers there is [serverless](https://conferences.oreilly.com/software-architecture/sa-eu/public/schedule/detail/71581). 
Serverless can be defined as the combination of BAAS (Backend-As-A-Service) and FAAS (Functions-As-A-Service). A BAAS provides 
backend features, like a database, search or chat to developers as an API. FAAS allows developers to implement business logic
without worrying about infrastructure. In this way, a complex product can be assembled by a front-end developer alone. 
While this approach allows unprecedentedly rapid time to market, one has to wonder whether this mix of individual services 
doesn't result in an end product that is un-viably expensive.