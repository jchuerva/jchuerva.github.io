---
title: EpicReact - Advanced React Hooks
date: 2022-09-15 16:00:00 +01:00
tags: [react, hooks, epicreact]
description: EpicReact - Advanced React Hooks
---

- [useReducer](#usereducer)
- [useCallback](#usecallback)
  - [Memoize](#memoize)
  - [useCallback](#usecallback-1)
- [useContext](#usecontext)
  - [Create a consumer hook](#create-a-consumer-hook)
- [useLayoutEffect](#uselayouteffect)
- [useImperativeHandle](#useimperativehandle)
- [useDEbugValue](#usedebugvalue)

# useReducer

It helps to separate the state logic from the components that make the state changes.
`useReducer` is usually preferable to `useState` when you have complex state logic that involves multiple sub-values or when the next state depends on the previous one.


syntax:
```js
const [count, setCount] = useReducer(reducer, initialArg, init);
```

`reducer` have two params:
 - "state" or the current value of count
 - "action" - what is passed to the `setCount` function

A more complex example that dispatch an object
```js
import * as React from 'react'

const countReducer = (state, newState) => newState

function Counter({initialCount = 0, step = 1}) {
  const [count, setCount] = React.useReducer(countReducer, initialCount)
  const increment = () => setCount(count + step)

  return <button onClick={increment}>{count}</button>
}

function App() {
  return <Counter />
}

export default App
```

Where we define the `countReducer` function with two variables, the **state** (`count`) and the **action** (`setCount`).

For example, we can pass a more complex object to the `setCount` function:
```js
import * as React from 'react'

function countReducer(state, action) {
  const {type, step} = action

  switch (type) {
    case 'increment': {
      return {
        ...state,
        count: state.count + step,
      }
    }
    default: {
      throw new Error(`Unsupported action type: ${type}`)
    }
  }
}

function Counter({initialCount = 0, step = 1}) {
  // the dispatch will call the countReducer function with the current state (`{count:}`) and the action (`{type:, step:}`)
  const [state, dispatch] = React.useReducer(countReducer, {
    count: initialCount,
  })
  const {count} = state
  const increment = () => dispatch({type: 'increment', step})

  return <button onClick={increment}>{count}</button>
}

function App() {
  return <Counter />
}

export default App
```

# useCallback
## Memoize

Memoization is something you can implement as a generic abstraction:

```js
function memoize<ArgType, ReturnValue>(cb: (arg: ArgType) => ReturnValue) {
  const cache: Record<ArgType, ReturnValue> = {}
  return function memoized(arg: ArgType) {
    if (cache[arg] === undefined) {
      cache[arg] = cb(arg)
    }
    return cache[arg]
  }
}

const addOne = memoize((num: number) => num + 1)
const getDog = memoize((name: string) => new Dog(name))
```

In React we have some hook for memoize: useCallback and useMemo

## useCallback

`useCallback` will return a memoized version of the callback that only changes if one of the dependencies has changed.

remember the `useEffect` dependency list?
```js
React.useEffect(() => {
  window.localStorage.setItem('count', count)
}, [count]) // <-- that's the dependency list
```

If you don't provide any, React will call your callback every render.

What if we use a function inside the `useEffect`? We need to include the function as a dependency.
```js
const updateLocalStorage = () => window.localStorage.setItem('count', count)
React.useEffect(() => {
  updateLocalStorage()
}, [updateLocalStorage]) // <-- function as a dependency
```

The problem is that since `updateLocalStorage`  is defined in the body, it's re-initialized and changes every render, to the `useEffect` is called every render! -> `useCallback` solves it!

```js
const updateLocalStorage = React.useCallback(
  () => window.localStorage.setItem('count', count),
  [count], // <-- yup! That's a dependency list!
)
React.useEffect(() => {
  updateLocalStorage()
}, [updateLocalStorage])
```

`useCallback` only renders if the dependency element has changed. If not, it gives you the same function done last time.

`useCallback` is just a shortcut to using `useMemo` for functions:

```js
// the useMemo version:
const updateLocalStorage = React.useMemo(
  // useCallback saves us from this annoying double-arrow function thing:
  () => () => window.localStorage.setItem('count', count),
  [count],
)

// the useCallback version
const updateLocalStorage = React.useCallback(
  () => window.localStorage.setItem('count', count),
  [count],
)
```

Articles to read:
- [When to useMemo and useCallback](https://kentcdodds.com/blog/usememo-and-usecallback)

# useContext

[Lift state] is a easy way to share state between components, but it can be a pain to pass props through many levels of components. For that we can use the `useContext` hook: we insert some state into a section of our react tree, and then extract that state anywhere within that react tree without having to explicitly pass it everywhere.

```js
import * as React from 'react'

const FooContext = React.createContext()
// we can provide a default value to the context, but it's not recommended 😉

function FooDisplay() {
  const foo = React.useContext(FooContext)
  return <div>Foo is: {foo}</div>
}

ReactDOM.render(
  <FooContext.Provider value="I am foo">
    <FooDisplay />
  </FooContext.Provider>,
  document.getElementById('root'),
)
// renders <div>Foo is: I am foo</div>
```

Let's see a more real component, as a wrapper component:

<details>
<summary>Incremental counter</summary>

```js
import * as React from 'react'

// 1. create the context
const CountContext = React.createContext()

// 2. create the provider component
function CountProvider(props) {
  // Always use the props as it a wrapper
  const [count, setCount] = React.useState(0)
  const value = [count, setCount]
  // we return value that it's an array ;)
  return <CountContext.Provider value={value} {...props} />
}

function CountDisplay() {
  // 3. use the context hook to consume the context values
  const [count] = React.useContext(CountContext)
  return <div>{`The current count is ${count}`}</div>
}

function Counter() {
  const [, setCount] = React.useContext(CountContext)
  const increment = () => setCount(c => c + 1)
  return <button onClick={increment}>Increment count</button>
}

function App() {
  return (
    <div>
      <CountProvider>
        <CountDisplay />
        <Counter />
      </CountProvider>
    </div>
  )
}

export default App
```

</details>

## Create a consumer hook

It's always good to create a custom hook to consume the context, so we can use it in any component.

In previous example, we can create a `useCount` hook:

```js
// custom hook
function useCount() {
  const context = React.useContext(CountContext)
  if (!context) {
    throw new Error('useCount must be used within a CountProvider')
  }
  return context
}

function CountDisplay() {
  // we can use the custom hook in all components
  const [count] = useCount()
  return <div>{`The current count is ${count}`}</div>
}
```

We can move `CountContext`, `CountProvider` and `useCount` to a separate file, and import it in any component.

# useLayoutEffect

(Not very used)

There are two ways to tell React to run side-effects after it renders:
- useLayoutEffect: If you need to mutate the DOM and/or do need to perform measurements
- useEffect: If you don't need to interact with the DOM at all or your DOM changes are unobservable (seriously, most of the time you should use this).

The difference about these is subtle (they have the exact same API), but significant. 99% of the time useEffect is what you want, but sometimes useLayoutEffect can improve your user experience.

# useImperativeHandle

(Not very used)

I needs the `forwardRef`

# useDEbugValue

Useful for debugging the custom hooks in the ReactDEvTools extension. It can be useful to give them a special label.

```js
// const formatDebugValue = ({initialCount, step}) => `\`${initialCount}\` => ${step}

function useCount({initialCount = 0, step = 1} = {}) {
  React.useDebugValue(`\`${initialCount}\` => ${step}`)
  // Or using the formatDebugValue
  // React.useDebugValue({initialCount, step}, formatDebugValue)
  const [count, setCount] = React.useState(initialCount)
  const increment = () => setCount(c => c + step)
  return [count, increment]
}
```
