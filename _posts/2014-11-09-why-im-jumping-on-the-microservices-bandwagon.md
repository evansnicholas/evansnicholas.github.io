---
layout: post
title: Why I'm jumping on the microservices bandwagon
---

This week I attended JFall, a large annual gathering of java programmers organized by the Dutch java user group (NLJUG).  Looking at the programme, I was especially excited about the chance to spent a couple of hours in a Docker workshop.  Docker being all the rage at the moment, I was looking forward to getting a structured introduction to it.  I had not expected however that this workshop would prove to be the missing jigsaw piece, the final connecting thread between a number of disparate themes that had been on my radar for a while.

I am an avid listener of Software Engineering Radio and until that Docker workshop I had not realised quite how intricately connected (and probably deliberately so!) the last few episodes have been: number 210 on microservices, 211 on continuous delivery, 212 on company culture and 213 again on microservices.  An impatient cynic might have dismissed these episodes as follows: microservices? Just a re-branded SOA.  Continuous delivery? Another talk about Jenkins.  Company culture? I thought Software Engineering Radio was meant to be technical!  However I now see how all these episodes combine to produce a clear vision for the future of enterprise software development.  And this conclusion is confirmed by Martin Fowler who, with the caution one would expect of someone who has seen endless hypes come and go, claims to "clearly think that the microservices architectural style is an important idea - one worth serious consideration for enterprise applications." 

### What is a microservice?

A microservice is characterised by a number of very familiar fundamental properties: it is small and manageable, it has one responsibility, it is loosely coupled to other pieces of software that it may need to engage with, it manages its own state (or better still: it is stateless), it has a well defined interface...  Sound like a well written, well designed (java) class? Or perhaps a service in a well designed, well implemented SOA?  Both of these software engineering constructs share these properties, but the microservices philosophy goes much further.  It takes what at first seem like purely technical considerations for good software design and draws radical conclusions about the organisational structure of software development teams in the enterprise.  

### How are microservices different from SOA?

Both the SOA and microservices approaches share the same desire to carve up a complicated business domain into small manageable chunks, which they also both call services.  However, beyond this shared initial goal they differ fundamentally in the technical realisation of these services and in the organisational structure they recommend for getting these services implemented.  

As far as technical implementation goes, SOA is often taken to be synonymous with ESB (Enterprise Service Bus).  An ESB is a monolithical platform where the services run and process messages that enter and leave the bus.  In this way, all services have to be written in the same programming language, and will run together in the same container.  The ESB is a heavy, all-or-nothing piece of machinery.  In stark contrast, the microservices approach recommends each service be its own independent program that does not even share a container with other services.  Microservices often expose their functionality via a web-based (often REST) protocol, and include their own lightweight web-server for processing requests.  This entails that two cooperating microservices do not even have to be written in the same programming language.  This total isolation of services means that dependency conflicts that may arise from having to run services in the same container are solved right from the outset.

As far as the organisation is concerned, SOA often entails SOA governance.  This is a scary term, with heavy centralized management connotations.  One imagines committees meeting at regular intervals to determine whether or not a particular service should be implemented.  Furthermore, SOA remains fairly silent about the split between development and operations teams, and where the responsibility lies for fixing services when they fail.  On all these points the microservices philosophy is very vocal.  It advocates a decentralized approach in which DevOps teams are end to end responsible for ensuring a microservice is created, runs and keeps running.  This is well captured by the motto (first popularized by Amazon): "if you build it, you run it".  In this way, much greater autonomy is granted to the teams to implement the service as they fit, using the technologies that they consider best suited.  And with this greater autonomy also comes a greater responsibility, namely to ensure that the service stays available.  

## And what's Docker got to do with it?

Finally I come to Docker, and that workshop at JFall.  As I started playing with Docker containers, scripting them to host a NodeJS instance and watching them start up in milliseconds, I realised that this was an incredibly powerful tool for facilitating the microservices vision.  This technology empowers a team to reliably create an independent piece of functionality that can run in a complicated microservices landscape.  It entails that a development team can be held responsible for the operational performance of its software.  And most importantly of all, it provides the tools for fulfilling the DevOps promise: operations and development can be united, without sacrificing either the creativity and messiness of development or the structure and orderliness of operations.  A development team's deliverable is no longer a JAR that still has to go through a long integration process before it reaches production but a fully operational unit of functionality that can be deployed anywhere and expected to work.

* * *

### References:
* Martin Fowler's [introduction](http://martinfowler.com/articles/microservices.html) to microservices
* Randy Shoup's [interview](http://www.se-radio.net/2014/10/episode-212-randy-shoup-on-company-culture/) on company culture and microservices
* Stefan Tilkov's [interview](http://www.se-radio.net/2014/09/episode-210-stefan-tilkov-on-architecture-and-micro-services/) on microservices 
* James Lewis' [interview](http://www.se-radio.net/2014/10/episode-213-james-lewis-on-microservices/) on microservices
* [What is Docker?](https://www.docker.com/whatisdocker/)

  




 