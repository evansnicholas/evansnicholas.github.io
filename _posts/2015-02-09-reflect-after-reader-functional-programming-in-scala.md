---
layout: post
title: Reflect after Reading - Functional Programming in Scala
---

Paul Chiusano and Rúnar Bjarnason describe *Functional Programming in Scala* as "the book we wish had existed when we were learning functional programming".  I can certainly echo this thought and proclaim that *Functional Programming in Scala* is the book I am glad exists as I am trying to learn functional programming.  The subject is vast, deep and has its roots in a branch of very abstract mathematics known as category theory.  Initial attempts to become acquainted with this programming style can be rather dispiriting.  However, as one progresses, one appreciates that "programming functionally" encompasses a wide spectrum of practices.  At one extreme it is, as Martin Odersky puts it in the foreword to the book, nothing more than programming in a "style that puts the focus on the functions in a program".  The term "function" is here to be understood in the mathematical sense of a "binary relation that maps arguments to results".  For more advanced practitioners, functional programming entails the usage of intimidatingly named patterns with names taken from category theory, such as "applicative functor" and "monad".  In trying to understand these patterns one is confronted with a barrage of seemingly irreconcilable information.  In one approach a monad is nothing but an interface offering a few methods.  In the other it is a typeclass, best understood in the context of Haskell.  In an attempt to provide a more intuitive approach, one also hears of monads described as a "computational context", or even a "programmable semi-colon" (wikiepedia).  The more mathematically inclined opt for purely abstract theoretical definitions.  

Fortunately, *Functional Programming in Scala* follows a well thought through pedagogy which clears the way through the confusion.  As the authors stress time and time again in the book, functional programming is a method one has to develop a feel for through repeated exercise (what they call "the importance of play").  They do not try to provide an "aha" definition of monads, in fact the term doesn't even appear in the book until chapter 3, 180 pages in.  Instead, the first two chapters are devoted to providing the reader exposure to the functional approach to programming.  By working through a series of case studies (a library for parallel computation, a property based testing framework and parser combinators), one slowly develops a sense for the re-occurring patterns which form the basis of functional programming.  Once this sense is developed, it becomes natural to give these patterns a name.  In this way, monoids, applicatives and even monads start to feel like old friends.   

## Focus on the functions

Chapter 1 introduces and motivates the core principle of functional programming: programs should be assembled using only *pure functions*.  A pure function is one which causes no *side effects*.  More formally, a pure function is one that is *referentially transparent*.  In other words, it can be reasoned about using the *substitution model*.  These concepts are the foundations on which the rest of the book builds.  The idea is that functions should always be replaceable by the value that they return.  This is not the case for a function that mutates an in-scope variable, or prints text to the console.

Functions with these properties are attractive because they provide reliable building blocks from which more complex programs can be constructed.  The fact that they can be substituted for their return values means that they are insulated from their environment.  This makes them modular, reusable, composable and testable.  Furthermore, such functions can be shown to obey certain laws, and these laws can be relied on when constructing larger programs.  This notion of the "algebra" of an API is one that returns frequently in the book.  

As clear cut as these ideas may seem at first, the authors of the book are careful to also add some nuance.  On closer inspection, the notion of a "side effect" is dependent on a notion of "observability": a function has no side effect if there are no *observable* consequences of running the function other than producing a return value.  But the range of what we consider observable is up for debate.  Are memory allocations on the heap performed by the function during its execution observable?  Can we dismiss log messages as unobservable if we do not consider them as an integral part of the program's functionality?  These questions tend towards the philosophical and do not undermine the usefulness of the concepts of purity and side effects.  Still it is good to be aware of their limitations.

## Functional Patterns

In chapter 2 the reader is guided towards creating API's that address certain general programming challenges.  During this process, similar methods make recurrent appearances: unit, map, map2, flatMap, sequence, traverse.  In chapter 3, these recurrent patterns are given a formal name and organized into abstractions that have their origins in category theory.

The first such abstraction to be discussed is the monoid:

{% highlight scala %}
trait Monoid[A] {
  def op(a1: A, a2: A): A
  def zero: A
}
{% endhighlight %}

While commenting on the meaning of a "monoid", in other words, while trying to answer the question: "what is a monoid?", the authors make some poignant comments on the difficulties of achieving an intuitive understanding of functional abstractions.  They write:

> A monoid is a type together with a binary operation (op) over that type, 
> satisfying associativity and having an identity element (zero).
>
> What does this buy us? Just like any abstraction, a monoid is useful to the
> extent that we can write useful generic code assuming only the capabilities
> provided by the abstraction.

The takeaway message here is that the value of an abstraction lies in its usefulness, not in its intuitive significance.  A monoid is therefore defined by a type, a set of operations on that type and a set of laws obeyed by those operations.  These operations and these laws are interesting because they reoccur often in useful programs.  To me, this pragmatic approach to understanding functional abstraction is very appealing, and it helps free oneself from the feeling that a proper "understanding" of functional programming involves associating an intuitive meaning to the abstractions.  This being said, searching for intuitive meaning is irresistible (and quite fun), and the authors do also indulge this temptation from time to time.  In the case of the monoid, the intuition is that it supports the notion of "addition".  In other words, data structures for which a monoid can be defined are foldable, or reduceable.  The monoid laws also imply that this reduction can happen in parallel on different sections of the data structure.  Typical monoidal data structures are lists or trees.

The next important functional abstraction in the functor:

{% highlight scala %}
trait Functor[F[_]] {
  def map[A,B](fa: F[A])(f: A => B): F[B]

  def distribute[A,B](fab: F[(A, B)]): (F[A], F[B]) =
    (map(fab)(_._1), map(fab)(_._2))

  def codistribute[A,B](e: Either[F[A], F[B]]): F[Either[A, B]] = e match {
    case Left(fa) => map(fa)(Left(_))
    case Right(fb) => map(fb)(Right(_))
  }
}
{% endhighlight %}

The defining operation for a functor is map.  Example functors are List and Option.  In and of itself, functor is not terribly interesting, but it is a step on the way to the monad:

{% highlight scala %}
trait Monad[F[_]] extends Functor[F] {
  def unit[A](a: => A): F[A]

  def flatMap[A,B](ma: F[A])(f: A => F[B]): F[B] =
    join(map(ma)(f))

  def map[A,B](ma: F[A])(f: A => B): F[B] =
    flatMap(ma)(a => unit(f(a)))

  def map2[A,B,C](ma: F[A], mb: F[B])(f: (A, B) => C): F[C] =
    flatMap(ma)(a => map(mb)(b => f(a, b)))

  def sequence[A](lma: List[F[A]]): F[List[A]] =
    lma.foldRight(unit(List[A]()))((ma, mla) => map2(ma, mla)(_ :: _))

  def traverse[A,B](la: List[A])(f: A => F[B]): F[List[B]] =
    la.foldRight(unit(List[B]()))((a, mlb) => map2(f(a), mlb)(_ :: _))

  def replicateM[A](n: Int, ma: F[A]): F[List[A]] =
    sequence(List.fill(n)(ma))

  def compose[A,B,C](f: A => F[B], g: B => F[C]): A => F[C] =
    a => flatMap(f(a))(g)

  def _flatMap[A,B](ma: F[A])(f: A => F[B]): F[B] =
    compose((_:Unit) => ma, f)(())

  def join[A](mma: F[F[A]]): F[A] = flatMap(mma)(ma => ma)

  def filterM[A](ms: List[A])(f: A => F[Boolean]): F[List[A]] =
    ms.foldRight(unit(List[A]()))((x,y) =>
      compose(f, (b: Boolean) => if (b) map2(unit(x),y)(_ :: _) else y)(x))
}
{% endhighlight %}

There are different possible choices for the minimal set of operations that characterize a monad:

* unit and flatMap
* unit and compose
* unit, map and join

Some examples of monads that are discussed in the book are Option, List, Gen (a generator of random data used in the property based testing framework), Parser (a type for processing input data used in the parser combinator framework), Reader, State, Id, IO (for performing io operations such as reading or writing to the console, or accessing a database) and ST (for working with mutable state). 

Once again, it is tempting to ask what all these data structures have in common and try to capture the essence of the monad in an intuitive way.  Although the same caution applies as in the case of the monoid, the authors do make a suggestion.  A feature of monads is that they support "for-comprehension" syntax.  A for-comprehension is syntactic sugar for a chain of flatMap and map calls.  Consider the following code for handling three Options:

{% highlight scala %}
option1 flatMap { value1 => 
  option2 flatMap { value2 => 
    option3 map { value3 =>
      doSomething(value1, value2, value3)
    }
  }
}
{% endhighlight %}

This can also be written:

{% highlight scala %}
for {
 value1 <- option1
 value2 <- option2
 value3 <- option3
} yield doSomething(value1, value2, value3)
{% endhighlight %}

The benefit of for-comprehension syntax is that is produces "flat" code, without the nesting of functions that makes the chained flatMap and map calls difficult to read.  Since for-comprehensions can always be rewritten to chained flatMap and map calls, all monads support it.  The authors use this syntax to motivate the following intuitive understanding of the monad:

> [a for-comprehension is] like an imperative program with statements that assign to variables, and *the monad specifies what occurs at statement boundaries*.  For example, with Id, nothing at all occurs except unwrapping and rewrapping in the Id constructor. With State, the most current state gets passed from one statement to the next. With the Option monad, a statement may return None and terminate the program. With the List monad, a statement may return many results, which causes statements that follow it to potentially run multiple times, once for each result.

It is this intuition which probably also lies behind the comparison of a monad with a "programmable semi-colon".

There is one last important functional abstraction addressed in the book, the applicative:

{% highlight scala %}

trait Applicative[F[_]] extends Functor[F] {

  def map2[A,B,C](fa: F[A], fb: F[B])(f: (A, B) => C): F[C] =
    apply(map(fa)(f.curried))(fb)

  def apply[A,B](fab: F[A => B])(fa: F[A]): F[B] =
    map2(fab, fa)(_(_))

  def unit[A](a: => A): F[A]

  def map[A,B](fa: F[A])(f: A => B): F[B] =
    apply(unit(f))(fa)

  def sequence[A](fas: List[F[A]]): F[List[A]] =
    traverse(fas)(fa => fa)

  def traverse[A,B](as: List[A])(f: A => F[B]): F[List[B]] =
    as.foldRight(unit(List[B]()))((a, fbs) => map2(f(a), fbs)(_ :: _))

  def replicateM[A](n: Int, fa: F[A]): F[List[A]] =
    sequence(List.fill(n)(fa))

  def factor[A,B](fa: F[A], fb: F[B]): F[(A,B)] =
    map2(fa, fb)((_,_))

  def product[G[_]](G: Applicative[G]): Applicative[({type f[x] = (F[x], G[x])})#f] = {
    val self = this
    new Applicative[({type f[x] = (F[x], G[x])})#f] {
      def unit[A](a: => A) = (self.unit(a), G.unit(a))
       
      override def apply[A,B](fs: (F[A => B], G[A => B]))(p: (F[A], G[A])) =
        (self.apply(fs._1)(p._1), G.apply(fs._2)(p._2))
    }
  }

  def compose[G[_]](G: Applicative[G]): Applicative[({type f[x] = F[G[x]]})#f] = {
    val self = this
    new Applicative[({type f[x] = F[G[x]]})#f] {
  
      def unit[A](a: => A) = self.unit(G.unit(a))
     
      override def map2[A,B,C](fga: F[G[A]], fgb: F[G[B]])(f: (A,B) => C) =
        self.map2(fga, fgb)(G.map2(_,_)(f))
    }
  }
 
  def sequenceMap[K,V](ofa: Map[K,F[V]]): F[Map[K,V]] =
    ofa.foldLeft(unit(Map[K,V]())) {
      case (acc, (k, fv)) => apply(map(acc)(m =>
        (n: Map[K,V]) => m ++ n))(map(fv)((v: V) => Map(k -> v)))
   }
}
{% endhighlight %}

It is interesting to note that an applicative implements many of the derived monadic operations (such as sequence and traverse), without implementing flatMap.  Instead, in an applicative unit and map2 form the minimal set of operations.  Since map2 can be implemented in terms of flatMap, all monads are applicatives.  However, flatMap cannot be implemented in terms of map2 and unit, and thus not all applicatives are monads.  Once again one is tempted to search for an intuitive formulation of the difference between these two abstractions.  Using the example of parsing, the authors provide some interesting insight.  The flatMap operation on parsers allows a particular parser to be chosen depending on the value parsed by another parser.  In contrast, the map2 operation allows two parsers to be combined into one, but without any possible interaction between the two parsers, in other words without one of the parsers having an effect on the other.  The authors summarize this difference as follows:

> Applicative constructs context-free computations, while Monad allows for context sensitivity.

## Embedded Domain Specific languages

The final chapter takes the functional approach and applies it to domains where it at first seems unsuited, such as IO, mutable state and stream processing.  The strength of this section is that it shows how an emphasis on pure functions can be valuable even in domains not typically associated with functional programming.  The core idea here is that a description of the computation to be performed is separated from the execution of the computation.  In this way, a model of a computation (a sort of abstract syntax tree) is built up using pure functions.  This model is then interpreted by another function, at which point the side effects are actually performed: text is actually printed to a console, a mutable variable is actually re-assigned, or a stream of data is processed.  However, by pushing the side effects to the "outer edges" of the program, one retains a core that is composed of pure functions, and therefore still profits from all the benefits of a functional approach.  

## Final thoughts
When I started reading *Functional Programming in Scala*, I thought of flatMap as primarily an operation on collections (lists or vectors).  In this context I understood how it worked, and it seemed a stretch to apply it in a more general and abstract way.  This situation has been turned on its head.  I now see flatMap as a general operation, with a particular instance in the case of a list or vector.  This shift in perspective is just the beginning, and I now feel equipped not only to dig deeper into the theory of functional programming, but also to apply it in my daily efforts as a programmer.    

* * *

### References
* An [interview](http://www.infoq.com/articles/functional-programming-scala-review) with Paul Chiusano and Rúnar Bjarnason
* An [introduction](http://adit.io/posts/2013-04-17-functors,_applicatives,_and_monads_in_pictures.html) to monads in pictures