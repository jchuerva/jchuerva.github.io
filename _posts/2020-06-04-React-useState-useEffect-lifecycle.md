---
title: React - useState, useEffect and component lifecycle
date: 2020-06-04 13:00:00 +01:00
tags: [React, Front-end, js]
description: React - useState, useEffect and component lifecycle
---

## Index

- [Index](#index)
  - [useState hook](#usestate-hook)
    - [Lazy initializer with useState](#lazy-initializer-with-usestate)
  - [useEffect hook](#useeffect-hook)
    - [Cleaning up effect](#cleaning-up-effect)
  - [React component lifecycle](#react-component-lifecycle)
  - [References](#references)


### useState hook

We need a place to put data that can change in our application, and we need to let React know when that state changes so it can update (or re-render) our app for us. 

In React, **state** is associated to components and when the state changes, the component is *updated*. To get access to this state and to update it, we use what is called the `useState` React Hook.

```
const [state, setState] = useState(initialState);
```

During the **initial render**, the returned state (*state*) is the same as the value passed as the first argument (*initialState*). During **subsequent re-renders**, the first value returned by useState will always be the most recent state after applying updates.

Example:
```js
function Greeting() {
  const [name, setName] = React.useState('')
  const handleChange = event => setName(event.target.value)
  return (
    <div>
      <form>
        <label htmlFor="name">Name: </label>
        <input onChange={handleChange} id="name" />
      </form>
      {name ? <strong>Hello {name}</strong> : "Please type your name"}
    </div>
  )
}
```

We pass it the default value of an empty string and react.useState returns an array with a stateful value, and a function to update it.


#### Lazy initializer with useState

It's important to remember that the *initialState* argument is the state used during the **initial render**. In subsequent renders, it is disregarded. 

If the initial state is the result of an expensive computation, you can provide a function as the initial value. That function will be called to retrieve the initial value. **That function will only be called when it's absolutely necessary to retrieve the initial value.** 


```js
const [state, setState] = useState(() => {
  const initialState = someExpensiveComputation(props);
  return initialState;
});
```

It's important to note that this function is called synchronously. It's expected to be synchronous.

Example:
```js
function Greeting() {
  const [name, setName] = React.useState(
    window.localStorage.getItem('name') || '',
  )
  console.log('rendered')
  React.useEffect(() => {
    window.localStorage.setItem('name', name)
  })
  ...
}
```

### useEffect hook

The function passed to `useEffect` will *run after every render*.

The hook receives two **arguments**: 
 - the function to execute 
 - an array with values that will be evaluated after each render, these values are called dependencies.
 
Depending on the second argument you have 3 options with different behavior. The logic for each option is:
- **If not present** the effect will run after every render. 
- **With an empty array ([])** the effect runs only once, after `mounting` and the first render
- **An array with values [a, b, c]** makes the effect evaluate the dependencies, whenever a dependency changes the effect will run

```js
// Option 1 - no dependencies
useEffect(() => {
  // heavy logic that runs after each render
});

// Option 2 - empty dependencies
useEffect(() => {
  // create an event listener, subscription, fetch one-time data
}, []);

// Option 3 - with dependencies
useEffect(() => {
  // fetch data whenever A, B or C changes.
}, [a, b, c]);
```

To sum-up the array options in the `useEffect`:
![image](/assets/images/2020-06-04-React-useState-useEffect-lifecycle/use-effect-dependency-otpions.jpg "useEfffect dependency options")


#### Cleaning up effect

`useEffects` creates resources that need to be cleaned up before the component leaves the screen. To do this, the function passed to `useEffect` may *return a clean-up function*. For example, to create a subscription:

```js
useEffect(() => {
  const subscription = props.source.subscribe();
  return () => {
    // Clean up the subscription
    subscription.unsubscribe();
  };
});
```

The *clean-up function* **runs before the component is removed** from the UI to prevent *memory leaks*. Additionally, if a component renders multiple times (as they typically do), the previous effect is cleaned up before executing the next effect.


### React component lifecycle

The chart that sum up the react component lifecycle:

![image](/assets/images/2020-06-04-React-useState-useEffect-lifecycle/hook-flow.png "React Hook Flow Diagram")
from [https://github.com/donavon/hook-flow](https://github.com/donavon/hook-flow)


### References
- [A visual guide to React Mental models, part 2: useState, useEffect and lifecycles](https://obedparla.com/code/a-visual-guide-to-react-mental-models-part-2-use-state-use-effect-and-lifecycles/)
- [The Beginner's Guide to React](https://egghead.io/courses/the-beginner-s-guide-to-react)
- [hook-flow](https://github.com/donavon/hook-flow)