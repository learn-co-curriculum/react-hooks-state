# React State

## Overview

In this lesson, we'll dive into component **state**.


## Objectives

1. Explain what state is
2. Explain the difference between state and props
3. Show examples of how state is defined and updated


## What's state?

Let's quickly talk about what _state_ is in React. State is data that is mutated
in your component. A component's state, unlike a component's props, _can_ change
during the component's life.

Consider the limitations of props: for a component's props to change, its
_parent_ component needs to send it new props (after which, the component would
'remake' itself with the new props). State provides us with a way to maintain and update information *within* a component _without_ requiring its parent to somehow
send updated information.

Imagine that we have a single component which displays an integer. When a user
clicks the component, it should increment its integer by 1. If we were to
represent this integer value in the component using **state**, the component
could increment itself without needing any fussy prop passing:

```js
class MyComp extends React.Component {

  // we use the constructor to set the INITIAL STATE
  constructor() {
    super()
    this.state = {
      count: 0
    }
  }

  // our increment method makes use of the 'setState' method, which is what we use to alter state
  increment = () => {
    const newCount = this.state.count + 1
    this.setState({
      count: newCount
    })
  }

  render() {
    return (
      <div onClick={this.increment}>
        {this.state.count}
      </div>
    )
  }
}
```

There's a lot going on above. Let's take some time to break things down. We set
up the initial value of state in the `constructor()`. Remember from Object Oriented
Javascript, the value of `this` above is an instance of our class `MyComp`. So
`this.state = {count: 0}` is saying this instance of `MyComp` should have a
property called `state` that has a value of `{count : 0}`. Also, we should call
`super()` in the constructor since we are inheriting from another class via the
`extends` keyword.

Also, handling events in React work a bit differently than in vanilla JavaScript.
We are going to dive deeper into React event handling later. For now, know that
React events are written as attributes inside a JSX tag and are named using
camelCase. Notice the `<div>` tag in the `render` method has an attribute
`onClick` which is set equal to the function object `this.increment`. What this code
does is:

1. When the `div` is clicked,
2. the function `this.increment` will be invoked,
3. thereby updating the state using `this.setState()`.

Take your time to read through the above code. Work through it line by line and
make sure you are comfortable before moving forward.


## Initial State and 'setState()'

Let's further explore both the setting of **initial state** in the constructor and the use of
**this.setState()**:

#### Initial State

In order to understand _why_ we are setting **initial state** in the constructor
the most important thing to know is that the `constructor` method runs **first**
when a component is made (and `render()` runs later!). Because we won't dive
further into the React Component Lifecycle just yet, commit this mantra to
heart:

> We set initial state in the constructor because it runs first

...got it? Good. Don't forget. Now forget we even mentioned this new concept of
'Component Lifecycle' and stay focused on state.

Once we have provided a component with its **initial state** we can manipulate
it later using the method: `setState()`.

#### `setState()`

`setState()` is straightforward in its purpose: it sets/updates state! That's
it! That's what it's there for. While simple in that way, there is one very
important caveat about _how_ it functions that we need to explore: it sets state
_asynchronously_.

In order to understand why this is important, let's look at an example of
`setState()` being used in a component. The following gif is of this component
(pay close attention to the `console.log()`s:

```js
class App extends Component {

  constructor() {
    super()
    this.state = {
      count: 0
    }
  }

  increment = () => {
    console.log(`before setState: ${this.state.count}`)

    this.setState({
      count: this.state.count + 1
    })

    console.log(`after setState: ${this.state.count}`)
  }

  render() {
    return (
      <div onClick={this.increment}>
        {this.state.count}
      </div>
    )
  }
}
```

<p align="center">
  <img src="https://curriculum-content.s3.amazonaws.com/react/asynchronous-state-setting-example.gif"/>
</p>

What we are seeing is `setState()` functioning _asynchronously_. When we execute
`setState()`, it is _non-blocking_. It fires off a message to the React
component's inner workings saying: "Hey, you need to update state to this value
when you have a chance." The component finishes doing its current task _before_
updating the state. In this case, it finishes executing the `increment` function
in full before updating the state.

It's not uncommon for new React developers to get 'bitten' by the asynchronous
nature of `setState()` at least once. If `setState()` were not _asynchronous_,
the two logs would not be the same number.


## A Word of Caution

While component state is a very powerful feature, it should be used as sparingly
as possible. State adds (sometimes unnecessary) complexity and can be very easy
to lose track of. The more state we introduce in our application, the harder it
will be to keep track of all of the changes in our data. Remember: state is for
values that are expected to change during the components life.


## Conclusion

- **state** is for values that are expected to change
- we assign **initial state** in the constructor
- we update state with `setState()`, which is a method provided by the `React.Component` class
- `setState()` is _asynchronous_

## Resources
- [Official React docs on state](https://facebook.github.io/react/docs/interactivity-and-dynamic-uis.html#components-are-just-state-machines)
- [Props vs. state](https://github.com/uberVU/react-guide/blob/master/props-vs-state.md)

<p class='util--hide'>View <a href='https://learn.co/lessons/react-initial-state'>Initial State</a> on Learn.co and start learning to code for free.</p>
