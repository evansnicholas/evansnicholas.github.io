---
layout: post
title: Reflect after Reading - Functional Programming in Scala
---

Paul Chiusano and Rúnar Bjarnason describe *Functional Programming in Scala* as "the book we wish had existed when we were learning functional programming".  I can certainly echo this thought and proclaim that *Functional Programming in Scala* is the book I am glad exists as I am trying to learn functional programming.  Functional programming is such a vast and deep subject that trying to learn it can feel rather dispiriting.  The more one learns, the more one appreciates that "programming functionally" is a sliding scale.  At one extreme, functional programming is, as Martin Odersky puts it in the Foreword to the book, nothing more than programming in a "style that puts the focus on the functions in a program".  The term "function" is here to be understood in the mathematical sense of a "binary relation that maps arguments to results".  For more advanced practioners, functional programming entails the usage of intimidatingly named patterns with their origins in the mathematics of category theory, such as applicative functors and monads.  And in trying to understand these patterns one is confronted with a barrage of seemingly irreconcilable information.  In one approach a monad is nothing but an interface offering a few methods.  In the other it is a typeclass, best understood in the context of Haskell.  In an attempt to provide a more intuitive approach, one also hears of monads described as a "computational context", or even a "programmable semi-colon" (wikiepedia).  The more mathematically inclined opt for the even more abstract theoretical definitions.  

Fortunately, *Functional Programming in Scala* follows a well thought through pedadogy which clears the way through the confusion.  As the authors stress time and time again in the book, functional programming is a method one has to develop a feel for through repeated exercise (what they call "the importance of play").  They do not try to provide an "aha" definition of monads, in fact the term doesn't even appear in the book until chapter 3, 180 pages in.  Instead, the first two chapters are devoted to providing the reader exposure to the functional method.  By working through a series of case studies (a library for parallel computation, a property based testing framework and parser combinators), one slowly develops a sense for the re-occuring patterns which form the basis of functional programming.  Once this sense is developed, it becomes natural to give these patterns a name.  In this way, monoids and applicatives start to feel like old friends.   

## Focus on the functions

Chapter 1 introduces and motivates the core principle of functional programming: programs should be assembled using only *pure functions*.  A pure function is one which causes no *side effects*.  More formally, a pure function is one that is *referentially transparent*.  In other words, it can be reasoned about using the *substitution model*.  These concepts are the foundations on which the rest of the book builds.  Without rehashing the book, the idea here is that functions should always be replaceable by the value that they return.  This is not the case for a function that mutates an in scope variable, or prints text to the console.

The reason that functions with these properties are so attractive is that they provide reliable building blocks from which more complex programs that be constructed.  The fact that they can be substituted for their return values means that they are insulated from their environment.  This makes them modular, reusable, composable and testable.  Futhermore, such functions can be shown to obey certain laws, and these laws can be relied on when constructing larger programs.  This notion of the "algebra" of an API is one that returns frequently in the book.  

As clear cut as these ideas may seem at first, the authors of the book are careful to also add some nuance.  On closer inspection, the notion of a "side effect" is dependent on a notion of "observability": a function has no side effect if there are no observable consequences of running the function other than producing a return value.  But the range of what we consider observable is up for debate.  Are memory allocations on the heap performed by the function during its execution observable?  Can we dismiss log messages as unobservable if we do not consider them as an integral part of the program's functionality?  These questions tend towards the philosphical and do not undermine the usefulness of the concepts purity and side effects.  Still it is good to be aware of them.

## Functional Patterns

In chapter 2 the reader is guided towards defining API's that address certain general programming challenges.  During this process, similar methods make recurrent appearences: unit, map, map2, flatMap, sequence, traverse.  In chapter 3, these recurrent patterns are given a formal name and place within the structures of functional programming that are based on the category theory.  These are summarized here:

{% highlight scala %}
sealed trait Test
case class Test1(message: String) {
  def write(string: String) = "This is a test"
}
{% endhighlight %}

In a sense, chapter 2 has already answered the question of why we should care about these structures: they are important because they reoccur.  They provide a basic infrastructure which programmers can turn to to improve their productivity.  However, it is difficult to resist the urge to ask the next question: but what is monad actually?  what do all instances of flatMap have in common?  Perhaps to some extent these are mistaken questions.  Still, Functional Programming in Scala does its best to provide an intuitive interpretation of these constructs (while adding the disclaimer that appeals to intuition are limited).  One that particularly appealed to me was the difference between an applicative and a monad.  The major structural difference between these two abstractions is the flatMap operator.  The intuitive explanation of this difference is [something to do with context...look this up in the book].

## Embedded Domain Specific languages

The final chapter takes the functional approach and applies it to domains where it at first seems unsuited, such as IO, mutable state and stream processing.  The strength of this section is that it shows how an emphasis on pure functions can be valuable even in domains typically associated with very different programming styles.  The core idea here is that a description of the computation to be performed is separated from the execution of the computation.  In this way, a model of a computation to be performed is build up using pure functions.  This model is then interpreted by another function, at which the point the side effects are actually performed: text is actually printed to a console, a mutable variable is actually re-assigned, or a stream of data is processed.  However, by pushing the side effects to the "outer edges" of the program, one retains a core that is composed of pure functions, and therefore still profits from all the benefits of a functional approach.  

## Final thoughts
When I started reading Functional Programming in Scala, I thought of flatMap as primarily an operation on collections (lists or vectors).  In this context I understood how it worked, and it seemed a stretch to apply it in a more general and abstract way.  This situation has now been turned on its head.  I now see flatMap as a general operation, the flatMap operation on a list or vector is nothing but a particular instance of the abstraction.  This shift in perspective is just the beginning, and I now feel equipped not only to dig deeper into the theory of functional programming, but also to apply in my daily efforts as a programmer.    

* * *

### References
* An [interview](http://www.infoq.com/articles/functional-programming-scala-review) with Paul Chiusano and Rúnar Bjarnason
* An [introduction](http://adit.io/posts/2013-04-17-functors,_applicatives,_and_monads_in_pictures.html) to monads in pictures