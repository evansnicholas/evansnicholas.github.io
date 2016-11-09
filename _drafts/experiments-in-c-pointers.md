---
layout: post
title: Experiments in C - Pointers
description: Getting to grips with the basics of pointers in C.
---
There is a view that "true" programmers understand pointers (for instance, see Joel on Software's [article](http://www.joelonsoftware.com/articles/theperilsofjavaschools.html) 
for an elaborate statement of this view).  By _pointer_ I mean an actual pointer, the kind that you can 
perform pointer arithmetic on, not simply a reference to an in-memory object (see this [stack overflow post](http://stackoverflow.com/questions/2629357/does-java-have-pointers) 
for a discussion of this point).  Armed with Kernighan and Richie's _The C Programming Language_, in this post I will
explore two of C's fundamental operators for the manipulation of pointers: <small>&</small> and <small>*</small> .

## Pointer basics

Kernighan and Richie define a pointer as follows:

> A pointer is a variable that contains the address of a variable.

This address is essentially a number whose size depends on the architecture of the computer on which the program 
is running (on a 64 bit machine, a pointer has a size of 64 bits, or 8 bytes).  As the diagram below shows, a pointer is
a number in memory that is interpreted as holding the address of a value (it "points" to the value).  The value can be anything: 
an integer, a string, an array, a struct... The word _value_ when applied to a pointer can be confusing.  From now on, I will use
the term _value of a pointer_ to denote the address contained by the pointer variable, and not the value that exists at that 
address (the value pointed to).

 <img src="/assets/images/pointers.png" class="img-responsive center-block" alt="Pointer illustration" /> 

In C a programmer can access and manipulate the value of a pointer. For instance, she can add one to a pointer, 
or divide it by two.  She can then look up the value that exists at the memory address just computed.  
This is powerful, but also dangerous.  An error in pointer arithmetic can lead to memory corruption and 
unexpected program crashes.


## Pointer experiments