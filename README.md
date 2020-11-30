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
// useState is a special HOOK from React that lets us use state in a function component
import React, { useState } from 'react'

function App() {
  // set up our initial state, and get the getter/setter for that piece of state
  const [count, setCount] = useState(0)

  // the increment function uses the setter function for the piece of state we're updating
  function increment() {
    setCount(count + 1)
  }

  // we can use the count variable to display the current value for this piece of state in our JSX
  return (
    <div onClick={increment}>
      {count}
    </div>
  )
}
```

There's a lot going on above. Let's take some time to break things down. 

We imported a special function from React called `useState`. This special function 
is a **React Hook** that will let us "hook into" React's internal state inside of 
our function component. 

When we call `useState(0)` inside the function component, that creates a new
"state variable" which our function gets access to. That new state variable has
an initial value of 0 (or whatever we pass into `useState` when we call it).

`useState` will return an array that has two things inside of it: a variable
that tells use the current value for that piece of state, and a _setter_
function so we can update that piece of state. It looks something like this:

```js
const countState = useState(0)
// => [0, setStateFunction]
const count = countState[0]
const setCount = countState[1]
```

To clean up the code, we can use [array destructuring][array destructuring MDN]
to save the two elements from that array to separate variables: `count` and
`setCount`:

```js
const [count, setCount] = useState(0)
```

We can then use the `count` variable to access that piece of state, and the
`setCount` function to update its value.

Also, handling events in React work a bit differently than in vanilla JavaScript.
We are going to dive deeper into React event handling later. For now, know that
React events are written as attributes inside a JSX tag and are named using
camelCase. Notice the `<div>` tag has an attribute `onClick` which is set equal to 
our `increment` function. What this code does is:

1. When the `div` is clicked,
2. the function `increment` will be invoked,
3. thereby updating the state of `MyComp` using `setCount()`.

Take your time to read through the above code. Work through it line by line and
make sure you are comfortable before moving forward.


## Setting state

The setter function we get back from calling `useState` is straightforward in its purpose: 
it sets/updates state! That's it! That's what it's there for. While simple in that way, 
there is one very important caveat about _how_ it functions that we need to explore: it sets state
_asynchronously_.

In order to understand why this is important, let's look at an example of
a state setter function being used in a component. The following gif is of this component
(pay close attention to the `console.log()`s:

```js
function App() {
  const [count, setCount] = useState(0)

  const increment = () => {
    console.log(`before setState: ${count}`)
    
    setCount(count + 1)
    
    console.log(`after setState: ${count}`)
  }

  return (
    <div onClick={increment}>
      {count}
    </div>
  )
}
```

<p align="center">
  <img src="https://curriculum-content.s3.amazonaws.com/react/asynchronous-state-setting-example.gif"/>
</p>

What we are seeing is `setCount()` functioning _asynchronously_. When we execute
`setCount()`, it is _non-blocking_. It fires off a message to the React
component's inner workings saying: "Hey, you need to update state to this value
when you have a chance." The component finishes doing its current task _before_
updating the state. In this case, it finishes executing the `increment` function
in full before updating the state.

It's not uncommon for new React developers to get 'bitten' by the asynchronous
nature of state setter functions at least once. If setting state were not
_asynchronous_, the two logs would not be the same number.


## A Word of Caution

While component state is a very powerful feature, it should be used as sparingly
as possible. State adds (sometimes unnecessary) complexity and can be very easy
to lose track of. The more state we introduce in our application, the harder it
will be to keep track of all of the changes in our data. Remember: state is for
values that are expected to change during the components life.


## Conclusion

- **state** is for values that are expected to change
- we create our initial state by calling the `useState` hook inside of our
  function components
- we update state with the setter function returned by useState
- setting state is _asynchronous_

## Resources
- [The useState hook](https://reactjs.org/docs/hooks-state.html)
- [Props vs. state](https://github.com/uberVU/react-guide/blob/master/props-vs-state.md)
- [Thinking in React](https://reactjs.org/docs/thinking-in-react.html#step-3-identify-the-minimal-but-complete-representation-of-ui-state)

<p class='util--hide'>View <a href='https://learn.co/lessons/react-initial-state'>Initial State</a> on Learn.co and start learning to code for free.</p>


[array destructuring MDN]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment