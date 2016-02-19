---
layout: post
title: Reflect after Reading - REST in Practice
---

*REST in Practice* by Jim Weber, Savas Parastatidis and Ian Robinson is a book about leveraging the web as a platform for application development.  Today the REST architectural style, recognizable by its dependence on URI's and HTTP verbs, is so ubiquitous that it seems almost impossible to imagine a world in which computers didn't communicate in this way.  However, the current dominance of the RESTful style makes it easy to forget that there are other standards for facilitating the exchange of information between computer systems, for instance SOAP and the associated WS-\* standards.  In the book, the authors argue that the REST style is superior because it leverages the infrastructure provided by the World Wide Web.  The success of the web at allowing the global sharing of information suggests that its underlying mechanisms should be applicable more broadly to the general problem of building of distributed systems.  In essence, the REST architectural style is a generalization of the principles underlying the web and was first described in Roy Fielding's PhD thesis *Architectural Styles and the Design of Network-based Software Architectures*.  In this work Fielding "generalized the Web's architectural principles and presented them as a framework of constraints, or an architectural style".  *REST in Practice* summarizes this architectural style and explains how to apply it to the problem of integrating services in an enterprise context.

## REST fundamentals

At the core of the RESTful style is a particular way of thinking about an application's domain model.  The domain is seen as constituted of **resources**.  A resource is anything that can be identified (can be assigned a unique id) and that can be given a representation.  The representation of a resource can be interpreted as describing the state of the resource at a particular point in time.  REST itself is agnostic as to how a resource should be represented, and a resource may have multiple representations.  Today, resource representations are often JSON, but JSON and REST don't have a fundamentally special relationship.  As far as REST is concerned, resources can be represented using XML, or encoded as JPEG, MP3 or a custom format.  The central idea in REST is that systems communicate by exchanging *representations of resources*.  This is also what REST stands for: **RE**presentational **S**tate **T**ransfer.   

Knowing a resource's state at a given point in time is a start, but in any interesting application resource states are going to evolve over time.  In Fielding's description of the RESTful architectural style resource state changes are driven by *hypermedia*.  Hypermedia is meta-information provided alongside a resource's representation which specifies links to other resources.  By interacting with these linked resources, a consumer of the application can advance the application state.  This principle is known as HATEOAS, **H**ypermedia **A**s **T**he **E**ngine **O**f **A**pplication **S**tate.  As the authors of the book explain, one of the great strengths of HATEOAS is that it allows a consumer to interact with an application without having any initial knowledge of how the application can behave.  Each interaction with the application is accompanied by hypermedia detailing the next possible interactions.  In this way the consumer is guided through the application flow by just-in-time instructions on how to proceed. 

## HTTP and the Richardson Maturity Model

## (Not) Leveraging the platform

## Beyond REST









* * *

### References
* Roy Fielding's [thesis](https://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm)
* Martin Fowler's description of the [Richardson Maturity Model](http://martinfowler.com/articles/richardsonMaturityModel.html)
