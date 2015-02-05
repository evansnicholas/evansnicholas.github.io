---
layout: post
title: Reflect after Reading - Functional Programming in Scala
---

Paul Chiusano and Rúnar Bjarnason describe *Functional Programming in Scala* as "the book we wish had existed when we were learning functional programming".  I can certainly echo this thought and proclaim that *Functional Programming in Scala* is the book I am glad exists as I am trying to learn functional programming.  Functional programming is such a vast and deep subject that trying to learn it can feel rather dispiriting.  The more one learns, the more one appreciates that "programming functionally" is a sliding scale.  At one extreme, functional programming is, as Martin Odersky puts it in the Foreword to the book, nothing more than programming in a "style that puts the focus on the functions in a program".  The term "function" is here to be understood in the mathematical sense of a "binary relation that maps arguments to results".  For more advanced practioners, functional programming entails the usage of intimidatingly named patterns with their origins in the mathematics of category theory, such as applicative functors and monads.  And in trying to understand these patterns one is confronted with a barrage of seemingly irreconcilable information.  In one approach a monad is nothing but an interface offering a few methods.  In the other is a typeclass, to be best understood in the context of Haskell.  In a more intuitive view it is a computation context, or even a programmable semi-colon.  Or the more mathematically inclined can opt for the even more abstract theoretical definition.  Fortunately, *Functional Programming in Scala* follows a well thought through pedadogy which clears the way through the confusion.



{% highlight scala %}
sealed trait Test
case class Test1(message: String) {
  def write(string: String) = "This is a test"
}
{% endhighlight %}