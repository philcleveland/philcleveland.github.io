---
layout: post
title: React Component Styles
date: 2017-01-16
---

I am really liking React so far.  In this post I am going to describe 4 different styles of creating components in React.  If you have any interest in starting with 
React, I highly recommend the courses by Corey House on [Pluralsight](https://www.pluralsight.com/).  They are amazing. Maybe the best Pluralsight author I have seen.  The courses
are amazingly thorough.  He walks the student through creating the development environment all the way to production deployment.  The styles I am describing below,
I learned from his courses.

At a high level there are only 2 types of components. There are stateless (i.e. Presentation) and stateful (i.e. Container) components. Overall you should strive to have far fewer Container
components than Presentation components.  Below I will show you how to implement Presentation and Container components in ES5 and ES6.

**Attributes of Container Components**
* Little or no markup
* Mostly logic
* Responsible for moving data to other components
* Normal to have local state
* Use an ES6 Class Component

**Attributes of Presentation Components**
* Mostly markup
* No local state. There components have state passed in via props from a container component
* Reusable. These components should not define how data is loaded or changed. Move that logic to the container component
* Use an ES6 Stateless Component

If you are using ES5.....why are you using ES5? Get setup with babel transpiling and polyfill so you can use the latest language features.  If for some reason you cannot
do that then here is a nice little snippet on how to create a React class component in ES5.

### ES5 Container Component 
{% highlight javascript linenos %}
    var MyES5ContainerComponent = React.createClass({
        getInitialState: function(){
            return {
                //put your state here
            };
        },
        render: function(){
            return (
                <h1>Hello</h1>
            );
        }
    });
{% endhighlight %}

### ES6 Container Component
Use this style when you need [local state](https://facebook.github.io/react/docs/state-and-lifecycle.html#adding-local-state-to-a-class).  Relatively speaking, you should likely have far fewer components
defined in this style than as **ES6 Presentation Components**.

You will also need to use this style when using [Lifecycle hooks](https://facebook.github.io/react/docs/state-and-lifecycle.html#adding-lifecycle-methods-to-a-class) (e.g. componentWillMount)

{% highlight javascript linenos %}
    class MyES6ContainerComponent extends React.Component {
        constructor(props){
            super(props);
            this.state = {
                //put your state here
            };
        }
        render() {
            return (
            <div>
                <h1>Hello, world!</h1>
            </div>
            );
        }
    }
{% endhighlight %}

### ES5 Stateless Presentation Component
These are easy to write.  They are just regular JavaScript functions that return some html markup.

{% highlight javascript linenos %}
    var MyES5PresentationComponent = function(props){
        return (
            <h1>Hello props.name</h1>
        );
    }
{% endhighlight %}

### ES6 Stateless Presentation Component

Any component that doesn't use local state and can be kept simple should probably use this style.  These components
are really easy to reason about.  They are also very easy to test.  Stick to this style for as many components
as possible.

**Use let or const instead of var.** This ensures the Component doesn't get accidentally reassigned.

Since a stateless Component is just a function, notice that I do not have to use the this keyword.
I also get to avoid having to use bind()
Don't use state in these methods. Pass it in as props. This style is best used for thin presentational components.
These components do not allow for lifecycle methods and if you get to a point where you think you need them
then it's probably best to change this to an **ES6 Class Component**

Avoid nesting functions as this can have negative performance implications.  If you find yourself nesting these
functions it's probably time to convert to an **ES6 Class Component**

{% highlight javascript linenos %}
    const MyES6PresentationComponent = (props) => {
        return (
            <h1>Hello ${props.name}</h1>
        );
    }

    // This is how to define required properties in ES6
    // in this case we are saying that props must have name
    // assigned as a string type.
    HelloWorld.propTypes = {
        name: React.PropTypes.string.isRequired
    };

    export default HelloWorld;
{% endhighlight %}

For more information on React components check out the React documentation for [Components and Props](https://facebook.github.io/react/docs/components-and-props.html)