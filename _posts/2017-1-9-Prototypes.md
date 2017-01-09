---
layout: post
title: Prototypes in JavaScript
date: 2017-1-9
---

First off, every object in JavaScript has a prototype object attached to it.  Objects inherit properties and methods from their prototype.  Prototypes also allow you to 
extend (i.e. [Monkey Patch](https://en.wikipedia.org/wiki/Monkey_patch)) existing objects.  Prototypes work kind of like extension methods in C# in that they attach 
functionality to all objects created of the specific type.  Whenever you create a new object in Javascript an empty prototype object is automatically created and linked to 
the new object.   The difference between a base class and a prototype is that a single prototype object serves all instances of the object.  This is important because you 
can accidently change a value on all objects if you are not careful and misuse the prototype. 

Here is an example, in JavaScript, of extending the Array class with first and last methods, which Array does not currently implement .  I will use the Array prototype object 
to add the new methods.

{% highlight javascript linenos %}
  Array.prototype.first = function() {
    return this[0];
  }

  Array.prototype.last = function() {
    return this[this.length - 1]
  }

  var arr = [1, 2, 3];
  var arr2 = [4, 5, 6];

  console.log(arr.first());
  console.log(arr.last());

  console.log(arr2.first());
  console.log(arr2.last());
{% endhighlight %}

Notice that I did not add the prototype per instance.  I added it once, but as you can see from the console output it was applied to both instances of the Array.

This next example will demonstrate how to add properties to a JavaScript object's prototype.

{% highlight javascript linenos %}
  Array.prototype.myName = "";

  var arr = [1, 2, 3];
  arr.myName = "arr";

  var arr2 = [4, 5, 6];
  arr2.myName = "arr2";

  console.log(arr.myName);
  console.log(arr2.myName);
{% endhighlight %}

I hope this helps solidify your knowledge of prototypes and how to add functionality to JavaScript objects via properties or methods.