---
layout: post
title: Reflect after Reading - REST in Practice
---

The web is the world's largest and most successful distributed computing system.  This premise is the starting point of the REST philosophy: what does the web teach us about building large distributed systems?  *REST in Practice* by Jim Weber, Savas Parastatidis and Ian Robinson is a book about leveraging the web as a platform for application development.  Today the REST architectural style, recognizable by its dependence on URI's and HTTP verbs, is so ubiquitous that it seems almost impossible to imagine a world in which computers didn't communicate in this way.  However, the current dominance of the RESTful style makes it easy to forget that there are other standards for facilitating the exchange of information between computer systems, such as SOAP and the associated WS-\* standards.  In the book, the authors argue that the REST style is superior to other integration solutions precisely because it leverages the infrastructure established and proven by the World Wide Web.  The REST architectural style has its roots in Roy Fielding's PhD thesis *Architectural Styles and the Design of Network-based Software Architectures*.  In this work Fielding "generalized the Web's architectural principles and presented them as a framework of constraints, or an architectural style".  *REST in Practice* summarizes this architectural style and explains how to apply it to the integration of systems in an enterprise context.

## REST fundamentals

At the core of the RESTful style is a particular way of thinking about an application's domain model.  The domain is seen as constituted of **resources**.  A resource is anything that can be identified (can be assigned a unique id) and that can be represented in some way.  The representation of a resource can be interpreted as describing the state of the resource at a particular point in time.  REST itself is agnostic as to how a resource should be represented, and a resource may have multiple representations.  Today, resource representations are often JSON, but JSON and REST don't have a fundamentally special relationship.  As far as REST is concerned, resources can be represented using XML, or encoded as JPEG, MP3 or a custom format.  The central idea in REST is that systems communicate by exchanging *representations of resources*.  This is also what REST stands for: **RE**presentational **S**tate **T**ransfer.   

Knowing a resource's state at a given point in time is a start, but in any interesting application resource states are going to evolve over time.  In Fielding's description of the RESTful architectural style resource state changes are driven by *hypermedia*.  Hypermedia is meta-information provided alongside a resource's representation which specifies links to other resources.  By interacting with these linked resources, a consumer of the application can advance the application state.  This principle is known as HATEOAS, **H**ypermedia **A**s **T**he **E**ngine **O**f **A**pplication **S**tate.  As the authors of the book explain, one of the great strengths of HATEOAS is that it allows a consumer to interact with an application without having any initial knowledge of how the application's behaviour.  Each interaction with the application is accompanied by hypermedia detailing the next possible interactions.  In this way the consumer is guided through the application flow by just-in-time instructions on how to proceed.

## HTTP and the Richardson Maturity Model

In a RESTful architecture, resources are identified by Uniform Resource Identifiers (URI's).  In the book, the authors use the example of a coffee shop to illustrate the various principles. For example, a particular order is identified by the URI *http://restbucks.com/order/1234*.  Interactions with resources are mediated through HTTP response/response cycles.  HTTP defines a select number of operations for interacting with resources: <small>GET</small>, <small>POST</small>, <small>PUT</small>, <small>DELETE</small>, <small>OPTIONS</small>, <small>HEAD</small>, <small>TRACE</small>, <small>CONNECT</small> and <small>PATCH</small>.  In this way, a <small>GET</small> request to the URI *http://restbucks.com/order/1234* returns a representation of the order with id 1234 (the authors use XML as their representation format):

{% highlight xml %}
<order xmlns="http://schemas.restbucks.com/order">
  <location>takeAway</location>
  <items>
    <item>
      <name>latte</name>
      <quantity>1</quantity>
      <milk>whole</milk>
      <size>small</size>
    </item>
  </items>
  <status>paid</status>
</order>
}
{% endhighlight %}

This order could be modified by sending a <small>PUT</small> request to the same URI with the new representation, for instance, where the milk type is changed from *whole* to *skimmed*.  

It may seem at first as if this small set of operations can never possibly support the variety of different interactions one is likely to encounter when building a real-world system.  However, it turns out that this selection of operations is more than sufficient (most of the operations are not even used in real-world systems).  In fact, rather than being a straight jacket, this pre-defined set of operations can be liberating because it frees the developer from having to worry about identifying and naming the operations that her system supports.  Similarly, the possible responses are also limited by the HTTP response codes (there are many more response codes than request verbs).  The response codes are divided into three ranges:

+ **100** range: Informational
+ **200** range: Successful
+ **300** range: Redirection
+ **400** range: Client Error
+ **500** range: Internal Server Error

Although these principles may seem simple enough, applying them consistently can be tricky.  Leonard Richardson developed a classification of REST-like systems that is very helpful in clarifying how the principles of REST should be applied.  This classification gives the developer building a RESTful system a framework from which she can think critically about her design and implementations choices.  The classification is known as the Richardson Maturity Model and consists of 4 levels:

+ **Level Zero**:
+ **Level One**:
+ **Level Two**:
+ **Level Three**: 


## (Not) Leveraging the platform

## Beyond REST









* * *

### References
* Roy Fielding's [thesis](https://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm)
* Martin Fowler's description of the [Richardson Maturity Model](http://martinfowler.com/articles/richardsonMaturityModel.html)
* [HTTP response codes](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)
