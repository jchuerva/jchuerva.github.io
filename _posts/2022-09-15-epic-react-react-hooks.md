---
title: EpicReact - React Hooks
date: 2022-09-15 09:00:00 +01:00
tags: [react, hooks, epicreact]
description: EpicReact - React Hooks
---

- [useState](#usestate)
  - [Lazy loader](#lazy-loader)
- [useEffect](#useeffect)
  - [Effect dependency](#effect-dependency)
  - [HTTP requests](#http-requests)
    - [Considerations](#considerations)
- [Custom Hooks](#custom-hooks)
- [Lifting state](#lifting-state)
- [useRef](#useref)
- [Error Boundaries](#error-boundaries)

------

# useState

```
 const [count, setCount] = React.useState(0)
```

- It accepts one argument
- It returns two values:
	- state value
	- function to update the state value (convention is to prefix by: `set`)


Example of a `useState` in a form:

```js
function Greeting({initialName = ''}) {
  const [name, setName] = React.useState(initialName)
  function handleChange(event) {
    setName(event.target.value)
  }

  return (
    <div>
      <form>
        <label htmlFor="name">Name: </label>
        <input onChange={handleChange} id="name" />
      </form>
      {name ? <strong>Hello {name}</strong> : 'Please type your name'}
    </div>
  )
}

function App() {
  return <Greeting initialName="Carlos" />
}
```

## Lazy loader

Load the default value the first time this component is rendered. We use a function in the default value.

Eg:
```js
const [name, setName] = React.useState(
    () => window.localStorage.getItem('name') || initialName,
  )
```

# useEffect

```js
React.useEffect(() => {
  // your side-effect code here.
  // this is where you can make HTTP requests or interact with browser APIs.
})
```

Eg.: Set the value of a localStorage item 
```js
React.useEffect(() => {
    window.localStorage.setItem('name', name)
  })
```


## Effect dependency

Instead of executing the `useEffect` every time, we can link it's execution to a variable value onChange. E.g: to the `name` variable (normally is a `useState` variable)

```js
React.useEffect(() => {
    window.localStorage.setItem('name', name)
  }, [name])
```

## HTTP requests

Doing the HTTP requests inside the `useEffect` hook allows us to ensure that whenever certain changes take place, we apply the side-effects based on those changes.

```js
React.useEffect(() => {
  async function effect() {
    const result = await doSomeAsyncThing()
    // do something with the result
  }
  effect()
})
```

Maybe it's easier to extract all the async code into a utility function which I call and then use the promise-based .then method instead of using async/await syntax:

```js
React.useEffect(() => {
  doSomeAsyncThing().then(result => {
    // do something with the result
  })
})
```

### Considerations

- handle errors
```js
fetchPokemon(pokemonName).then(
  pokemon => setPokemon(pokemon),
  error => setError(error),
)
```
- use status better that too many if/else.
Eg:
  - idle: no request made yet
  - pending: request started
  - resolved: request successful
  - rejected: request failed

An easy way to manage the `status` and the values, we could storage it together in an Object:
```js
const [state, setState] = React.useState({
  status: 'idle',
  pokemon: null,
  error: null,
})

const {status, pokemon, error} = state

// and to update each property
setState({status: 'resolved', pokemon})
```

In future, this could be done with the `useReducer` hook


# Custom Hooks

We can extract the `useState` and `useEffect` hooks, into a new function Hook.

E.g: A hook that get and set the value of a local storage item
```js
function useLocalStorageState(key, defaultValue = '') {
  const [state, setState] = React.useState(
    () => window.localStorage.getItem(key) || defaultValue,
  )

  React.useEffect(() => {
    window.localStorage.setItem(key, state)
  }, [key, state])

  return [state, setState]
}
```

in our app, we can use this custom hook:
```js
const [name, setName] = useLocalStorageState('name', initialName)

function handleChange(event) {
    setName(event.target.value)
  }
```


# Lifting state

How to share state between two sibling components? The idea is to find the first common parent and placing the state management there.

In `App` component, we place the state management for the three components `FavoriteAnimal`, `Name` and `Display`.

Note how we use the `onChange` to pass the function that change the value of the state management.

```js
function Name({name, onNameChange}) {
  return (
    <div>
      <label htmlFor="name">Name: </label>
      <input id="name" value={name} onChange={onNameChange} />
    </div>
  )
}

function FavoriteAnimal({animal, onAnimalChange}) {
  return (
    <div>
      <label htmlFor="animal">Favorite Animal: </label>
      <input id="animal" value={animal} onChange={onAnimalChange} />
    </div>
  )
}

function Display({name, animal}) {
  return <div>{`Hey ${name}, your favorite animal is: ${animal}!`}</div>
}

function App() {
  const [animal, setAnimal] = React.useState('')
  const [name, setName] = React.useState('')
  return (
    <form>
      <Name name={name} onNameChange={event => setName(event.target.value)} />
      <FavoriteAnimal
        animal={animal}
        onAnimalChange={event => setAnimal(event.target.value)}
      />
      <Display name={name} animal={animal} />
    </form>
  )
}
```

# useRef

After a component has been rendered, it’s considered “mounted.” That’s when the React.useEffect callback is called.
So often you’ll do direct DOM interactions/manipulations in the `useEffect` callback.

- we need to create the `const tiltRef = React.useRef()`
- in the elm, we need to add the `ref={tiltRef}` to associate it
- in `useEffect` hook, use the `useRef` to the element created


```js
function Tilt() {
  const tiltRef = React.useRef()

  React.useEffect(() => {
    const tiltNode = tiltRef.current // const {current: tiltNode} = tiltRef

    // add a new class to the useRef element
    tiltNode.classList.add('carlos')
  }, [])

  return (
    <div ref={tiltRef} className="tilt-root">
      Ola k ase
    </div>
  )
}
```


# Error Boundaries

It's a simple way to handle errors.
Check the [react-error-boundary](https://github.com/bvaughn/react-error-boundary) package.
