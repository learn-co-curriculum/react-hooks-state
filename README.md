# React State

## Overview

In this lesson, we'll dive into component **state**, and see how we can make our
components respond to change dynamically by working with the React state system.

## Objectives

1. Explain what state is
2. Explain the difference between state and props
3. Show examples of how state is defined and updated

## What's state?

Let's quickly talk about what **state** is in React. State is data that is
**dynamic** in your component. A component's state, unlike a component's props,
_can_ change during the component's life.

Consider the limitations of props: for a component's props to change, its
**parent** component needs to send it new props (after which, the component
would 'remake' itself with the new props). State provides us with a way to
maintain and update information _within_ a component _without_ requiring its
parent to somehow send updated information.

Imagine that we have a single component which displays an integer. When a user
clicks the component, it should increment its integer by 1. If we were to
represent this integer value in the component using **state**, the component
could increment its own state and automatically re-render whenever state is
updated!

## useState

In order to work with these special **state variables** in our components, we
must first import a function from React called `useState`. This special function
is a **React Hook** that will let us "hook into" React's internal state inside
of our function component.

```js
import React, { useState } from "react";
```

### Initializing State

Once we've imported the `useState` hook, we can call it inside of our component,
like so:

```js
import React, { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  return <button>{count}</button>;
}
```

When we call `useState(0)` inside the function component, that creates a new
"state variable" which our function gets access to. That new state variable has
an **initial value** of 0 (or whatever we pass into `useState` when we call it).

`useState` will return an **array** that has two things inside of it:

- `count`: the current value for the state variable
- `setCount`: a _setter_ function so we can update the state variable

We could access those elements from the array individually, like this:

```js
const countState = useState(0);
// => [0, setStateFunction]
const count = countState[0];
const setCount = countState[1];
```

But to clean up the code, React recommends using
[array destructuring][destructuring] to achieve the same result in one line of
code instead of three:

```js
const [count, setCount] = useState(0);
```

We can then use the `count` variable to access that piece of state and display
its current value in the `<button>` element.

## Setting State

The setter function we get back from calling `useState` is straightforward in
its purpose: it sets/updates state! That's it! That's what it's there for.
Whenever we want to update state, we can just call the setter function (in our
case, `setCount`):

```js
function Counter() {
  const [count, setCount] = useState(0);

  function increment() {
    setCount(count + 1);
  }

  return <button onClick={increment}>{count}</button>;
}
```

Now, when the `<button>` element is clicked, it will run our `increment`
function. `increment` calls the `setCount` function to do these two things:

- Update the value of `count` to be `count + 1`
- Re-render our component

The magic of working with **state** is that we don't have to worry about any
complex DOM manipulation (like finding the button element and telling it to
display the new `count` value) &mdash; whenever we call the `setCount` function,
React will automatically re-render our component, along with any of its child
components, and update the DOM based on the new values for state!

Using state like this allows React to be very performant: based on which
component is updated, React can determine which child components are affected
and how the DOM needs to be changed when these components are re-rendered.

Take your time to read through the above code. Work through it line by line and
make sure you are comfortable before moving forward.

### Setting State is Asynchronous

While using the `setCount` function is straightforward enough, there is one very
important caveat about _how_ it functions that we need to explore: it sets state
**asynchronously**.

In order to understand why this is important, let's look at an example of a
state setter function being used in a component. The following gif is of this
component (pay close attention to the `console.log()`s:

```js
function Counter() {
  const [count, setCount] = useState(0);

  function increment() {
    console.log(`before setState: ${count}`);

    setCount(count + 1);

    console.log(`after setState: ${count}`);
  }

  return <div onClick={increment}>{count}</div>;
}
```

<p align="center">
  <img src="https://curriculum-content.s3.amazonaws.com/react/asynchronous-state-setting-example.gif"/>
</p>

What we are seeing is `setCount()` functioning **asynchronously**. When we
execute `setCount()`, it is _non-blocking_. It fires off a message to the React
component's inner workings saying: "Hey, you need to update state to this value
when you have a chance." The component finishes doing its current task _before_
updating the state. In this case, it finishes executing the `increment` function
in full before updating the state.

It's not uncommon for new React developers to get 'bitten' by the asynchronous
nature of state setter functions at least once. If setting state were not
_asynchronous_, the two logs would not be the same number.

For this reason, React recommends using a slightly different syntax for setting
state when working with values that are calculated based on the previous version
of state (like our counter). To demonstrate the issue, consider the following:

```js
function Counter() {
  const [count, setCount] = useState(0);

  function increment() {
    // call setCount twice, to update the counter by two every time we click
    setCount(count + 1);
    setCount(count + 1);
  }

  return <div onClick={increment}>{count}</div>;
}
```

This is a contrived example &mdash; we could just as easily have called
`setCount(count + 2)` instead of calling `setCount` twice. But if you run this
example in your browser, you may be surprised at the result. Instead of seeing
the counter incremented by two, it's still only incremented by 1!

This problem makes more sense if we add some logs back in:

```js
function increment() {
  console.log(`before setState: ${count}`);
  setCount(count + 1);
  console.log(`after setState once: ${count}`);
  setCount(count + 1);
  console.log(`after setState twice: ${count}`);
}
```

Even though we call `setCount` multiple times, the value of `count` isn't
updated immediately!

As mentioned before, setting state is not synchronous. In our example, calling
`setCount(count + 1)` will evaluate to `setCount(1)` in _both_ cases:

```js
function increment() {
  console.log(count);
  // => 0

  setCount(count + 1);
  // equivalent to setCount(0 + 1)

  console.log(count);
  // => 0

  setCount(0 + 1);
  // equivalent to setCount(0 + 1)

  console.log(count);
  // => 0
}
```

React actually provides a built in solution for this problem. Instead of passing
a new value into `setCount`, we can also pass a callback function. That
function, when called inside `setCount` will be passed the state variable from
when that `setCount` was called. This is typically referred to as the _previous
state_. With this knowledge, we can rewrite the `increment` function to:

```js
function increment() {
  setCount((currentCount) => currentCount + 1);
  setCount((currentCount) => currentCount + 1);
}
```

When using the callback version of `setCount`, React will pass in the current
value of `count` before updating it. Now our code works as intended and updates
`count` by 2 when the button is clicked.

As a rule of thumb, **any time you need to set state based on the current value
of state, you should use the callback syntax**.

## A Word of Caution

While component state is a very powerful feature, it should be used as sparingly
as possible. State adds (sometimes unnecessary) complexity and can be very easy
to lose track of. The more state we introduce in our application, the harder it
will be to keep track of all of the changes in our data. Remember: **state is
only for values that are expected to change during the components life**.

## Conclusion

Whenever we need _dynamic_ data in our applications (values that change over
time), we should use **state**. We create our initial state by calling the
`useState` hook inside of our components.

To update state, we must use the `setState` function returned by `useState`, so
that changes to state cause our components to re-render.

Also, setting state is _asynchronous_, so any updates to state that are based on
the current value of state should be made using the callback syntax.

## Resources

- [The useState hook](https://reactjs.org/docs/hooks-state.html)
- [Props vs. State](https://github.com/uberVU/react-guide/blob/master/props-vs-state.md)
- [Thinking in React](https://reactjs.org/docs/thinking-in-react.html#step-3-identify-the-minimal-but-complete-representation-of-ui-state)

[destructuring]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment
