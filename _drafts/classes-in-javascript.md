---
layout: post
title: The curious case of classes in ES6
description: Trying to understand ES6 classes with the help of the babel transpiler and Professional Javascript for Web Developers by Nicholas Zakas
---

Classes are an important abstraction mechanism in many object-oriented
programming languages.  They are informally described as "blueprints" for the creation of
objects and can be related to each other by inheritance relationships.  Although ECMAScript is often classified as an object-oriented language,
it has an awkward relationship with the class concept.  In [Professional
Javascript for Web Developers](http://www.amazon.com/Professional-JavaScript-Developers-Nicholas-Zakas/dp/1118026691)
Nicholas Zakas goes so far as to claim that "ECMAScript [pre-ES6] has no concept of classes".
However, the ES6 version of the language introduces the _class_ keyword.
Although this may seem like a fundamental change, in reality it isn't.  The ES6 _class_
keyword is nothing more than syntactic sugar for the object construction and prototypical
inheritance mechanisms that were already present in earlier versions of ECMAScript.
By studying how [babel](http://babeljs.io/) transpiles ES6 classes to ES5 we will relate
the _class_ keyword to the traditional object creation and prototypical inheritance
techniques that Zakas describes in his book.

<strong>NB</strong>: In order to be able to focus on the transpilation of ES6 classes and not be distracted by
other artifacts of the ES6 to ES5 transpilation we will only use the babel
[transform-es2015-classes plugin](http://babeljs.io/docs/plugins/transform-es2015-classes/).
This means that the transpiled code will still contain certain ES6 features, such as import
statements.

## Object Creation

Zakas describes a pattern he calls the "combination Constructor/Prototype pattern"
for object creation in ES5.  In this pattern, object properties are added
to the new instance of an object whereas methods are added to the object's prototype:

{% highlight javascript %}
function Person(name) {
  this.name = name;
  this.friends = ["Ross", "Phoebe"];
}

Person.prototype = {
  constructor: Person,
  sayName: function()  {
    console.log(this.name);
  }
}
{% endhighlight %}

In ES6, you could rewrite this code using the _class_ keyword:

{% highlight javascript %}
export default class Person {
  constructor(name) {
    this.name = name;
    this.friends = ["Ross", "Phoebe"];
  }

  sayName() {
    console.log(this.name);
  }
}
{% endhighlight %}

Transpiling this ES6 class definition to ES5 using babel we discover that
the resulting code can be seen as an application of Zakas' "combination Constructor/Prototype pattern".

{% highlight javascript %}
import _classCallCheck from "babel-runtime/helpers/classCallCheck";
import _createClass from "babel-runtime/helpers/createClass";

let Person = function () {
  function Person(name) {
    _classCallCheck(this, Person);

    this.name = name;
    this.friends = ["Ross", "Phoebe"];
  }

  _createClass(Person, [{
    key: "sayName",
    value: function sayName() {
      console.log(this.name);
    }
  }]);

  return Person;
}();

export default Person;
{% endhighlight %}

The __classCallCheck_ and __createClass_ methods are helper methods from
the [babel-runtime](https://github.com/thejameskyle/babel-handbook/blob/master/translations/en/user-handbook.md#babel-runtime) module, and are defined as follows:

{% highlight javascript %}
var _createClass = function () {
  function defineProperties(target, props) {
    for (var i = 0; i < props.length; i++) {
      var descriptor = props[i];
      descriptor.enumerable = descriptor.enumerable || false;
      descriptor.configurable = true;
      if ("value" in descriptor) descriptor.writable = true;
      Object.defineProperty(target, descriptor.key, descriptor);
    }
  }

  return function (Constructor, protoProps, staticProps) {
    if (protoProps) defineProperties(Constructor.prototype, protoProps);

    if (staticProps) defineProperties(Constructor, staticProps);

    return Constructor;
  };
}();

function _classCallCheck(instance, Constructor) {
  if (!(instance instanceof Constructor)) {
    throw new TypeError("Cannot call a class as a function");
  }
}
{% endhighlight %}

In the transpiled code we observe that the Person constructor function is
exactly the same as the one we wrote in ES5.  The __createClass_ method is
responsible for adding the method definitions present in the Person ES6 class
to the Person prototype.  Note that the top-level _Parent_ function is immediately
invoked, which means that the __createClass_ method is only called once, when the module
is loaded.

## Inheritance

In ES5, Zakas describes a way of using prototypical inheritance
he refers to as the "Parasitic Combination Inheritance pattern".  The main
objectives of this pattern are to maximize method reuse while minimizing property sharing.
Furthermore, Zakas tries to achieve this in an elegant way, without unnecessary
object creation and property overriding.


{% highlight javascript %}
function object(o){
  function F(){}
  F.prototype = o;
  return new F();
}

function inheritPrototype(subType, superType){
  var prototype = object(superType.prototype);
  prototype.constructor = subType;
  subType.prototype = prototype;
}

function Person(name){
  this.name = name;
  this.friends = ["Ross", "Phoebe"];
}

Person.prototype.sayName = function(){
  console.log(this.name);
};

function Policeman(name){
  SuperType.call(this, name);
  this.title = "Officer";
}

inheritPrototype(Policeman, Person);

Policeman.prototype.sayTitle = function(){
  console.log(this.title);
};

{% endhighlight %}

In "Parasitic Combination Inheritance", the super class constructor is called once
to ensure the sub class inherits the super class' properties.  The super class' methods are
inherited by ensuring the sub class' prototype is set to equal an object whose
prototype is equal to the super class' prototype.

In ES6, classes can inherit properties and methods through the _extends_ keyword.
In this way, the relationship between a Person and a Policeman could be expressed
as follows:

{% highlight javascript %}
import Person from "./Person";

export default class Policeman extends Person {
  constructor(name, title) {
    super(name);
    this.title = "Officer";
  }

  sayTitle() {
    console.log(this.title);
  }
}
{% endhighlight %}

Transpiling to ES5 with babel produces the following:

{% highlight javascript %}
import _Object$getPrototypeOf from "babel-runtime/core-js/object/get-prototype-of";
import _classCallCheck from "babel-runtime/helpers/classCallCheck";
import _createClass from "babel-runtime/helpers/createClass";
import _possibleConstructorReturn from "babel-runtime/helpers/possibleConstructorReturn";
import _inherits from "babel-runtime/helpers/inherits";
import Person from "./Person";

let Policeman = function (_Person) {
  _inherits(Policeman, _Person);

  function Policeman(name) {
    _classCallCheck(this, Policeman);

    var _this = _possibleConstructorReturn(this, _Object$getPrototypeOf(Policeman).call(this, name));

    _this.title = "Officer";
    return _this;
  }

  _createClass(Policeman, [{
    key: "sayTitle",
    value: function sayTitle() {
      console.log(this.title);
    }
  }]);

  return Policeman;
}(Person);

export default Policeman;
{% endhighlight %}

Of the imported helper methods, __inherits_ is the most interesting:

{% highlight javascript%}
function _inherits(subClass, superClass) {
  if (typeof superClass !== "function" && superClass !== null) {
    throw new TypeError("Super expression must either be null or a function, not " + typeof superClass);
  }
  subClass.prototype = Object.create(superClass && superClass.prototype, {
    constructor: {
      value: subClass,
      enumerable: false,
      writable: true,
      configurable: true
    }
  });

  if (superClass) Object.setPrototypeOf ? Object.setPrototypeOf(subClass, superClass) : subClass.__proto__ = superClass;
}
{% endhighlight %}

Here we see that the sub class' prototype property is set to equal a new object whose prototype is the super class's
prototype (this ensures the sub class inherits the super class' methods).  This is the same mechanism as used in Zakas' pattern.  Moreover, the _setPrototypeOf_ method is used
to assign the super class' constructor as the sub class' prototype (I am still actually a little confused about the
difference between setting the prototype property and using the _Object.setPrototypeOf_ method).  This constructor is then explicitly called in the
sub class' constructor to ensure the sub class acquires all the properties that are defined on the super class.

## Conclusion
The takeaway message from these investigations is that ES6 classes do not introduce
any new concept of "class" to ECMAScript.  The class syntax simply makes it easier for
developers to use constructor functions and prototypical inheritance.  However, there is a
growing segment of the javascript community that feels that inheritance is an abstraction
mechanism to be avoided.  They consider the ES6 class mechanism a "bad part"
because it encourages developers to use a feature of the language that they should stay away from.
This rejection of inheritance is popular amongst developers inspired by the functional programming
paradigm.  When programming in that way, method reuse is achieved through composition (mixins) rather than
inheritance.


### References
* Nicholas Zakas' [Professional Javascript for Web Developers](http://www.amazon.com/Professional-JavaScript-Developers-Nicholas-Zakas/dp/1118026691)
* A curated [list](https://github.com/joshburgess/not-awesome-es6-classes) of resources critical of ES6 classes
* The [babel](http://babeljs.io/) transpiler
