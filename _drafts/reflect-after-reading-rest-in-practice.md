---
layout: post
title: Reflect after Reading - REST in Practice
description: Description
---

The web is the world's largest and most successful distributed computing system.  This premise is the starting point of the REST philosophy: what does the web teach us about building large distributed systems?  *REST in Practice* by Jim Weber, Savas Parastatidis and Ian Robinson is a book about leveraging the web as a platform for application development.  Today the REST architectural style, recognizable by its dependence on URI's and HTTP verbs, is so ubiquitous that it seems almost impossible to imagine a world in which computers didn't communicate in this way.  However, the current dominance of the RESTful style makes it easy to forget that there are other standards for facilitating the exchange of information between computer systems, such as SOAP and the associated WS-\* standards.  In the book, the authors argue that the REST style is superior to other integration solutions precisely because it leverages the infrastructure established and proven by the World Wide Web.  The REST architectural style has its roots in Roy Fielding's PhD thesis *Architectural Styles and the Design of Network-based Software Architectures*.  In this work Fielding "generalized the Web's architectural principles and presented them as a framework of constraints, or an architectural style".  *REST in Practice* summarizes this architectural style and explains how to apply it to the integration of systems in an enterprise context.

## REST fundamentals

At the core of the RESTful style is a particular way of thinking about an application's domain model.  The domain is seen as constituted of **resources**.  A resource is anything that can be identified (can be assigned a unique id) and that can be represented in some way.  The representation of a resource can be interpreted as describing the state of the resource at a particular point in time.  REST itself is agnostic as to how a resource should be represented, and a resource may have multiple representations.  Today, resource representations are often JSON, but JSON and REST don't have a fundamentally special relationship.  As far as REST is concerned, resources can be represented using XML, or encoded as JPEG, MP3 or a custom format.  The central idea in REST is that systems communicate by exchanging *representations of resources*.  This is also what REST stands for: **RE**presentational **S**tate **T**ransfer.   

Knowing a resource's state at a given point in time is a start, but in any interesting application resource states are going to evolve over time.  In Fielding's description of the RESTful architectural style resource state changes are driven by *hypermedia*.  Hypermedia is meta-information provided alongside a resource's representation which specifies links to other resources.  By interacting with these linked resources, a consumer of the application can advance the application state.  This principle is known as HATEOAS, **H**ypermedia **A**s **T**he **E**ngine **O**f **A**pplication **S**tate.  As the authors of the book explain, one of the great strengths of HATEOAS is that it allows a consumer to interact with an application without having any initial knowledge of the application's behaviour.  Each interaction with the application is accompanied by hypermedia detailing the next possible interactions.  In this way the consumer is guided through the application flow by just-in-time instructions on how to proceed.

## HTTP and the Richardson Maturity Model

In a RESTful architecture, resources are identified by Uniform Resource Identifiers (URI's).  In the book, the authors use the example of a coffee shop to illustrate the various principles. For example, a particular order is identified by the URI *http://restbucks.com/order/1234*.  Interactions with resources are mediated through HTTP request/response cycles.  HTTP defines a select number of operations for interacting with resources: <small>GET</small>, <small>POST</small>, <small>PUT</small>, <small>DELETE</small>, <small>OPTIONS</small>, <small>HEAD</small>, <small>TRACE</small>, <small>CONNECT</small> and <small>PATCH</small>.  Furthermore, these verbs are associated with certain conventions governing their semantics.  For instance, <small>GET</small> requests are expected to be *side effect free* and *idempotent*.  A <small>GET</small> request to the URI *http://restbucks.com/order/1234* returns a representation of the order with id 1234 (the authors use XML as their representation format):

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
{% endhighlight %}


This order could be modified by sending a <small>PUT</small> request to the same URI with the new representation, for instance, where the milk type is changed from *whole* to *skimmed*.  

It may seem at first as if this small set of operations can never possibly support the variety of different interactions one is likely to encounter when building a real-world system.  However, it turns out that this selection of operations is more than sufficient (most of the operations are not even used in real-world systems).  In fact, rather than being a straightjacket, this pre-defined set of operations can be liberating because it frees the developer from having to worry about identifying and naming the operations that her system supports.  Similarly, the possible responses are also limited by the HTTP response codes (there are many more response codes than request verbs).  The response codes are divided into five ranges:

+ **100** range: Informational
+ **200** range: Successful
+ **300** range: Redirection
+ **400** range: Client Error
+ **500** range: Internal Server Error

Although these principles may seem simple enough, applying them consistently can be tricky.  Leonard Richardson developed a classification of REST-like systems that is very helpful in clarifying how the principles of REST should be applied.  This classification gives the developer building a RESTful system a framework from which she can think critically about her design and implementations choices.  The classification is known as the Richardson Maturity Model and consists of 4 levels:

+ **Level Zero Service**:  The service exposes only one URI and supports only one HTTP verb (often <small>POST</small>).  
+ **Level One Service**: The service exposes many URI's but still supports only one HTTP verb.  Although Level One services can appear RESTful, they often fall foul of piggybacking a Remote Procedure Call (RPC) protocol on top of HTTP rather than being truly RESTful.  A characteristic symptom of such design is the use of verbs in URIs.  In such a service, URIs come to identify operations on a server rather than resources.  The practice of using the variable parts of a URI to pass parameters to a remote method is known as *URI tunneling*.  When designing a RESTful service it is important to remember that URIs should identify resources and that operations should be expressed using HTTP verbs.
+ **Level Two Service**: The service exposes many URI's and supports different HTTP verbs on each URI.  Many RESTful API's are level two services.
+ **Level Three Service**:  In addition to satisfying the criteria for being a level two service, it includes hypermedia in its resource representations.  This hypermedia guides a consumer in its interactions with the service, in accordance with the HATEOAS principle.

## Leveraging the platform

The fundamentals of a RESTful architecture are the modeling of an application's domain in terms of resources and using the HTTP request/response cycle to interact with those resources.  In addition to verbs and response codes, HTTP provides additional infrastructure in the form of headers.  These headers can be used to address various aspects of the implementation of a distributed system.  

### Caching

An HTTP request/response cycle is expensive as it involves, in its simplest form, a roundtrip from the consumer to the server.  The completion of this roundtrip may also involve the server performing some heavy computation in order to complete the request.  HTTP provides a number of headers that can be used to address this issue, such as: <small>Expires</small>, <small>Cache-Control</small>, <small>ETag</small> and <small>Last-Modified</small>.  

The semantics of a <small>GET</small> request (that it should be side-effect free and idempotent) make it especially amenable to caching.  These semantics entail that, as long as the representation of the resource hasn't changed between requests, it doesn't matter whether a <small>GET</small> actually hits the origin server.  If they can assume the representation hasn't changed, intermediate proxies can unburden the origin server by handling the request for it.  The <small>Expires</small> and <small>Cache-Control</small> headers enable such caching by defining a "freshness lifetime" for resource representations.  As long as a representation is "fresh", it can be used to serve a <small>GET</small> request.  This caching mechanism can be made even more efficient by providing an easy way to re-evaluate whether a resource representation is still fresh, even after it has officially expired.  The <small>ETag</small> and <small>Last-Modified</small> headers implement such a mechanism by "tagging" a particular resource representation with an identifier.  In this way, a cache can ask a server for the latest tag of a particular resource, and if the server responds with the same tag, the cache can extend the resource's freshness.  If the tag has changed, the cache knows it must update its representation.  Similarly this mechanism can be used by the origin server to avoid performing expensive re-computations of resource representations.  If a <small>GET</small> request contains the <small>ETag</small> of the currently held representation, the server can use this value to determine whether it must recompute the resource representation, or whether it can respond with a <small>304 Not Modified</small> response.

### Concurrency

An unavoidable scenario that must be handled by any distributed system is the case that several consumers try to modify the state of a resource simultaneously and incompatibly.  As in the case of caching, HTTP headers can be used to handle these situations.  We have already seen that a given resource representation can be identified by a tag (known as an <small>ETag</small>).  This information can be combined with the following HTTP headers to implement a form of concurrency control known as *optimistic locking*: <small>If-Match</small>, <small>If-None-Match</small>, <small>If-Modified-Since</small>, <small>If-Unmodified-Since</small>.  An HTTP request that includes one of these headers is known as a *conditional request* as it will only be handled by the server if the condition expressed in the header holds.  <small>ETag</small> values can be used as content for the conditional headers.  This ensures that a request is only handled by the server if the resource is still in the state expected by the consumer.  If this is not the case, the server can respond with a <small>409 Conflict</small> response, or a <small>412 Precondition Failed</small>.  Note that for this type of concurrency control to work properly, the server itself must still be implemented in the correct way.  For instance, if a server handles multiple requests in a non-thread safe way, it could give consumers the illusion that their conditional requests have been handled adequately whereas in reality this is not the case.

### Hypermedia

While they do experiment with their own custom hypermedia format, the authors of *REST in Practice* eventually settle on *Atom* as the definitive hypermedia format for their RESTful coffee shop.  In Atom, resource representations are structured as lists called *feeds* containing *entries*.  Atom feeds are expressed in XML and enrich the pure data content of a resource representation with meta-data.  One of the most important meta-data tags is the <small>&lt;atom:link></small> tag, which allows a resource representation to link to other resources, thereby implementing the basic premise of HATEOAS.  A <small>&lt;atom:link></small> tag has several attributes that enable its interpretation:

+ **rel**: Describes the link's semantics.
+ **href**: The URI of the linked resource.
+ **type**: The media type (the format) of the linked resource's representation.

Obviously, the ability of a consumer to actually use such meta-data is dependent on whether it can recognize the link's *rel* attribute and subsequently perform an appropriate action.  Humans perform such interpretative acts all the time (for instance by clicking on links in webpages), but for machines this requires sophisticated capabilities.  The usage of hypermedia controls by computers is therefore likely to involve them being pre-loaded with knowledge of possible link relations and associated behaviour.  When represented using atom, the order representation described earlier will look like this:

{% highlight xml %}
<entry xmlns="http://www.w3.org/2005/Atom">
  <id>urn:uuid:2eab5193-fd83-417d-91b3-266684d4ff5b</id>
  <title type="text">order</title>
  <updated>2016-02-03T12:00:01Z</updated>
  <author>
    <name>Jenny</name>
  </author>
  <link rel="self"
        href="http://restbucks.com/order/1234"/>
  <link rel="payment"
        href="http://restbucks.com/payment"
        type="application/vnd.restbucks+xml" />
  <content type="application/vnd.restbucks+xml">
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
  </content>
</entry>
{% endhighlight %}

### Security

When implementing a safe and secure system it is helpful to be able to lean on mature proven technology.  When it comes to security, HTTP provides a number of tried and tested mechanisms, the most important and widely used of which is HTTPS.  HTTPS works by sending HTTP requests and responses over TLS (Transport Level Security) rather than TCP (Transmission Control Protocol).  HTTPS ensures the HTTP requests and responses are sent over an encrypted channel.  HTTPS also ensures that consumers and servers can trust the parties they are communicating with (that the receiving/sending parties really are who they claim to be).  In this way, HTTPS is said to provide *confidentiality* and *integrity*.

The authors of *REST in Practice* discuss a number of other web based security mechanisms, such as the signing of HTTP request bodies and the use of the OAuth and OpenID protocols.  Web Security is a domain that is rapidly evolving and unfortunately the book does not discuss the more recents developments, such as OAuth 2 and JSON Web Tokens.  Nevertheless, the main premise still holds: the web is an extremely hostile environment, and security mechanisms that have survived in this environment are likely to be worth using, also in an enterprise context.

### SOAP and WS-\*

As we mentioned at the start, REST is not the only architectural style for integrating systems.  Another popular style is the Web Services (WS-\*) stack and the associated SOAP protocol.  The authors acknowledge that proponents of the RESTful style tend to be very critical of the WS-\* stack.  While the book tries to avoid explicitly taking sides in this debate, it does provides some arguments for the bad blood.  The fundamental objection they raise against the WS-\* stack is that it treats HTTP as nothing more than a transport protocol and ignores many of the infrastructural features that it provides (and that were discussed above).  For instance, the SOAP protocol piggybacks on HTTP and ignores the richness of the HTTP verbs, response codes and headers.  In this way, most WS-\* based services would be categorized as Level Zero services in Richardson's Maturity Model.  Furthermore, they are highly critical of the WSDL standard which promotes RPC like communication between services, which is total anathema to the core philosophy of REST.   

## Beyond REST

The highest level in Richardson's Maturity Model is characterized by its use of hypermedia to connect resource representations.  This meta-data guides a consumer in her interactions with the system.  But what if we wanted the consumer not only to follow links, but also to be able to understand, reason and answer questions about the content of the representations?  Extracting the semantics of a representation would be expected of a human consumer, but how could a computer consumer achieve this?  Enabling the semantic interpretation of resource representations is the aim of a number of technologies that fall under the umbrella term *Semantic Web* (such as RDF, OWL, SPARQL...).  These technologies can be used to enhance resource representations with further meta-data.  This meta-data can then be processed by computer systems in a generic way and allows them to infer statements about the content of the representations.  These are ambitious technologies still very much under development.  In Richardson's terms, we could describe services that enable the semantic interpretation of resource representations as new Level Four services.  Such services are also sometimes referred to as an ingredient of "Web 3.0".  Time will tell whether these technologies gain traction.  The beauty of the web is that it grows organically, and this makes it exciting, unpredictable and a fascinating subject of study.

* * *

### References
* Roy Fielding's [thesis](https://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm)
* Martin Fowler's description of the [Richardson Maturity Model](http://martinfowler.com/articles/richardsonMaturityModel.html)
* [HTTP response codes](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)
* [HTTP headers](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)
* [Optimistic Locking](https://en.wikipedia.org/wiki/Optimistic_concurrency_control)
* [Atom syndication format](https://tools.ietf.org/html/rfc4287)
