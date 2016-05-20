---
layout: post
title: The curious case of classes in ES6
description: Description
---

Classes are an important abstraction mechanism in many so-called object-oriented
programming languages.  Although Javascript is often classified as an object-oriented language, it has an awkward relationship with the class concept.  In _Professional
Javascript for Web Developers_ Nicholas Zakas goes so far as to claim that "ECMAScript
has no concept of classes" (p173).  This claim is made in the context of a pre-ES5 version of ECMAScript.  However, ES5 introduced the _class_ keyword into the language.
Although this may seem like a fundamental change, in reality it isn't.  The ES5 _class_
keyword is nothing more than syntactic sugar for the constructions that already
were present in earlier versions of ECMAScript.  By looking at how Babel transpiles
ES6 classes, we will explore how the new _class_ keyword fits into standard
javascript constructions.

## Object Creation

Zakas describes a pattern he calls the "combination Constructor/Prototype pattern"
for object creation in pre-ES6 ECMAScript.  In this pattern, properties are added
to the new object whereas methods are added to the prototype:

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

If we transpile the following ES6 class definition to ES5 using, we observe
that the _class_ construct is indeed syntactic sugar for the
"combination Constructor/Prototype pattern".  The ES6 class definition is:

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

and when compiled to ES5 using babel, we obtain the following:

{% highlight javascript %}
"use strict";

Object.defineProperty(exports, "__esModule", {
  value: true
});

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

var Person = function () {
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

exports.default = Person;
{% endhighlight %}

In the compiled code we observe that the Person constructor function is
exactly the same of the one we wrote directly in ES5.  The _createClass method is
responsible for adding the method definitions present in the Person ES6 class
to the Person prototype.

## Inheritance

In ES6, classes can inherit properties and methods through the _extends_ keyword.
In this way, a Policeman class can extend a Person class:

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
"use strict";

Object.defineProperty(exports, "__esModule", {
  value: true
});

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

var _Person2 = require("./Person");

var _Person3 = _interopRequireDefault(_Person2);

function _interopRequireDefault(obj) {
  return obj && obj.__esModule ? obj : { default: obj };
}

function _classCallCheck(instance, Constructor) {
  if (!(instance instanceof Constructor)) {
    throw new TypeError("Cannot call a class as a function");
  }
}

function _possibleConstructorReturn(self, call) {
  if (!self) {
    throw new ReferenceError("this hasn't been initialised - super() hasn't been called");
  }

  return call && (typeof call === "object" || typeof call === "function") ? call : self;
}

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

var Policeman = function (_Person) {
  _inherits(Policeman, _Person);

  function Policeman(name) {
    _classCallCheck(this, Policeman);

    var _this = _possibleConstructorReturn(this, Object.getPrototypeOf(Policeman).call(this, name));

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
}(_Person3.default);

exports.default = Policeman;

{% endhighlight %}

Turning back to Zakas' exposition of inheritance pre-ES6, we see that the babel
transpilation is actually an implementation of what Zakas calls "Parasitic Combination Inheritance":

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

inheritPrototype(SubType, SuperType);

Policeman.prototype.sayTitle = function(){
  console.log(this.title);
};

{% endhighlight %}

In "Parasitic Combination Inheritance", the super type constructor is called once
to ensure the sub type inherits the super types properties.  The super types methods are inherited by ensuring the subtype's prototype is set to equal an object whose prototype is equal to the super type's prototype.

## Conclusion
