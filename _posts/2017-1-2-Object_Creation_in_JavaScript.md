---
layout: post
title: Object Creation in JavaScript
---

A note before starting. **use strict** prevents a lot of syntax errors that
normally would be accepted and helps prevent bonehead errors. Use it!

**use strict** is a literal expression ignored by JS versions prior to ECMA 5
and must go at the beginning of your script file or at the beginning of a function.


```
'use strict';

//can be scoped like this as well
function f(){
  'use strict'
  ...
}

```

### Using Object Literals

Create a type on the fly. In a dynamic language there is no need to pre-define the class.

{% highlight javascript linenos %}
  var knight = {
    name: 'Sir Reginald',
    color: 'White',
    weapon: 'Longsword'
  };

  // Here I can add new properties, even after creating the class
  knight.age = 24;

  // Here is how I can add a function to the class after creation
  knight.attack = function() {
    console.log('I shall smite thee in the name of the king!');
  };

{% endhighlight %}

### Constructor Functions
Here is how to define the class with a function, also known as a constructor
function. This likely looks a bit more familiar to anyone coming from a statically
typed language like C#.

{% highlight javascript linenos %}

  function Knight(name, color, weapon) = {
    this.name = name
    this.color = color
    this.weapon = weapon
    attack = function() {
      console.log('I shall smite thee in the name of the king!');
    }
  };

  // Here is how you create a new instance of Knight.
  var sirReginald = new Knight('Sir Reginald', 'White', 'Longsword');
  sirReginald.attack();

{% endhighlight %}

Notice the use of the **new** key word in line 11. It creates a new empty object.  When
using **this** inside of the constructor function **this** refers to the new
empty object. This is important to remember because without using the **new**
keyword the function would still run just fine. It would just set the properties
on whatever object is currently executing.  That might be the 'global' object
or the 'window' object in a web browser.

### ECMA 6 Class Definition

If you are coming from a statically typed language this will likely feel very
familiar to how you are used to defining classes.

{% highlight javascript linenos %}

  class Knight {
    constructor(name, color, weapon){
      this.name = name
      this.color = color
      this.weapon = weapon
    }

    attack(){
      console.log('I shall smite thee in the name of the king!');
    }
  }

  // Here is how you create a new instance of Knight. This part is no different
  // than how it is done with a constructor function.
  var sirReginald = new Knight('Sir Reginald', 'White', 'Longsword');
  sirReginald.attack();

{% endhighlight %}

### Final Thoughts
I personally prefer the ECMA 6 method of creating classes. It has more ceremony
than, for instance, object literals... but hey I am coming from a statically
typed environment.  

Just remember all of these methods of object creation are really just syntactic
sugar for [Object.create](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/create), which I will leave to the reader to research and determine whether or not you
like the syntax.  [Object.create](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/create) is more powerful since it allows finer control over property attributes. I will likely
be covering property attributes and their uses in an upcoming post.
