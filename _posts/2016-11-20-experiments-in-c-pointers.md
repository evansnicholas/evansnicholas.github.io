---
layout: post
title: Experiments in C - Pointers
description: Getting to grips with the basics of pointers in C.
---
There is a view that "true" programmers understand pointers (see Joel on Software's [article](http://www.joelonsoftware.com/articles/theperilsofjavaschools.html) 
for an elaborate statement of this view).  By _pointer_ I mean an actual pointer, the kind that you can 
perform pointer arithmetic on, not simply a reference to an in-memory object (see this [stack overflow post](http://stackoverflow.com/questions/2629357/does-java-have-pointers) 
for a discussion of the difference).  Armed with Kernighan and Ritchie's _The C Programming Language_, in this post I
explore two of C's fundamental operators for the manipulation of pointers: <small>&</small> and <small>*</small> .

## Pointer basics

Kernighan and Ritchie define a pointer as follows:

> A pointer is a variable that contains the address of a variable.

This address is essentially a number whose size depends on the architecture of the computer on which the program 
is running (on a 64 bit machine, an address has a size of 64 bits, or 8 bytes).  As the diagram below shows, a pointer is
a number in memory that is interpreted as holding the address of a value (it "points" to the value).  The value can be any data type
supported in C: an integer, a char, an array, a struct... The word _value_ when applied to a pointer can be confusing.  It can be used to denote the value of 
the address contained by the pointer variable, or the value that exists at that address (the value pointed to).  In what follows, I will do my best to use the term
_value_ unambiguously. 

 <img src="/assets/images/pointers.png" class="img-responsive center-block" alt="Pointer illustration" /> 

In C a programmer can access and manipulate the value of the address held by a pointer. For instance, she can add one to this value, 
or divide it by two.  She can then look up the data structure that exists at the memory address so computed.  This is powerful, but also dangerous.  An error in pointer arithmetic can lead to memory corruption and 
unexpected program crashes.  Pointers are especially necessary in C because all function arguments are passed by value.  This means
that calling a function causes copies of the function's arguments to be made.  In order to avoid excessive copying, functions 
can be called with pointers to variables, rather than the actual (copied) values of these variables.


## Manipulating pointers in C

C offers two important operators for pointer manipulation: <small>&</small> and <small>*</small>.  The <small>*</small>
symbol is also used to declare a variable that holds a pointer.  These operators enable the programmer
to navigate between the value of the address and the data structure that exists at that address.  The direction of this navigation
is illustrated in the following diagram.

<img src="/assets/images/pointer-operators.png" class="img-responsive center-block" alt="Pointer operators" />

<small>&</small> takes us from value to address, while <small>*</small> takes us from address to value (this is known
as _dereferencing_).

## Experiments in pointer manipulation

Some simple pointer manipulation:

{% highlight c %}
#include <stdio.h>

int main()
{
    /* Declare the variable x to have value 1. */
    int x = 1;
    /* Declare a pointer p that points to an int. */ 
    int *p; 
    /* Assign to p the address at which the value of x is stored. */
    p = &x;
    /* Change the value at the address held by p to 2. */
    *p = 2; 
    /* x is 2 */
    printf("x is %d\n", x); 
}
{% endhighlight %}

Pointers and arrays:

{% highlight c %}
#include <stdio.h>

int main()
{
    /* a is an array of 10 ints. */
    int a[10];
    /* Set the first element to 1. */
    a[0] = 1;
    /* The array is actually a pointer to the first element. */
    /* &a[0] and a are equal: 1 */
    printf("&a[0] and a are equal: %d\n", &a[0] == a);
    /* Compute the address of the second element. */
    int *a1 = &a[0] + 1;
    /* Set the value of the second element to 2. */
    *a1 = 2;
    /* The second element is: 2 */
    printf("The second element is: %d\n", a[1]);
}
{% endhighlight %}

Pointers can also point to values that are themselves pointers:

{% highlight c %}
#include <stdio.h>

int main()
{
    /* Declare two arrays of ints. */
    int a[10];
    int b[10];
    /* Set the first elements of the arrays to 1 and 10. */
    a[0] = 1;
    b[0] = 10;
    /* 
       Declare an array that holds pointers to arrays of ints.
       Remember: a pointer to an array is actually 
       just a pointer to the first element. 
    */
    int *p[2];
    /* Store pointers to the arrays a and b in the array */
    p[0] = a;
    p[1] = b;
    /* p is a pointer to a pointer to an int. */
    int **a1 = p;
    /* 
       Doubly dereferencing this pointer returns the 
       value of the first element of a.
    */
    /* The first element of a is: 1 */ 
    printf("The first element of a is: %d\n", **a1);
    /* 
       Doubly dereferencing the second element of p 
       returns the value of the first element of b.
    */
    /* The first element of b is: 10 */ 
    printf("The first element of b is: %d\n", **(a1 + 1));
}
{% endhighlight %}

The following is an illustration of this final situation:

<img src="/assets/images/pointer-to-pointer.png" class="img-responsive center-block" alt="Pointer to pointer illustration" />

