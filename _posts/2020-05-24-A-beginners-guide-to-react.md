---
title: Notes from the The Beginner's Guide to React course
date: 2020-05-24 12:00:00 +01:00
tags: [React, Front-end, js]
description: Notes from the The Beginner's Guide to React course
---

## Index
- [Index](#index)
  - [Course Link](#course-link)
  - [UNPK](#unpk)
  - [JSX syntax](#jsx-syntax)
  - [Fragment element](#fragment-element)
  - [Reusable React Component](#reusable-react-component)
  - [Props types validations:](#props-types-validations)
  - [Interpolation in JSX](#interpolation-in-jsx)
  - [Style (css)](#style-css)
  - [Event handlers \& useState](#event-handlers--usestate)
  - [useEffect](#useeffect)
  - [Lazy initializer with useState](#lazy-initializer-with-usestate)
  - [useEffect](#useeffect-1)
  - [Custom hook](#custom-hook)
  - [React Hook Flow](#react-hook-flow)
  - [Basic Forms](#basic-forms)
  - [Dynamic forms](#dynamic-forms)
  - [Controlling Form Values](#controlling-form-values)
  - [Eror Boundaries](#eror-boundaries)
  - [Rendering a List](#rendering-a-list)
  - [Lifting State](#lifting-state)
  - [HTTP Requests](#http-requests)
  - [React DevTools](#react-devtools)


### Course Link
Course: [The Beginner's Guide to React](https://egghead.io/courses/the-beginner-s-guide-to-react) course.


### UNPK

Use [UNPK](https://unpkg.com/) to quickly load any file/npm package using a URL
Eg: load React in a html:

```html
<body>
  <div id="root"></div>
  <!-- load React using unpkg. It's usinig the development version -->
  <script src="https://unpkg.com/react@16.12.0/umd/react.development.js"></script>
  <script src="https://unpkg.com/react-dom@16.12.0/umd/react-dom.development.js"></script>
  <!-- load our script -->
  <script type="text/javascript">
    const rootElement = document.getElementById('root')
    // create the React element
    const element = React.createElement('div', {
      className: 'container',
      children: 'Hello World',
    })
    // render the React element in the HTML element
    ReactDOM.render(element, rootElement)
  </script>
</body>
```

We can use the `production` package of `React` (eg to see the performance):
```html
<script src="https://unpkg.com/react@16.12.0/umd/react.production.min.js"></script>
<script src="https://unpkg.com/react-dom@16.12.0/umd/react-dom.production.min.js"></script>
```


### JSX syntax

Why? More simple and similar syntax to HTML syntax

```html
<body>
  <div id="root"></div>
  <script src="https://unpkg.com/react@16.12.0/umd/react.development.js"></script>
  <script src="https://unpkg.com/react-dom@16.12.0/umd/react-dom.development.js"></script>
  <!-- need babel to compile -->
  <script src="https://unpkg.com/@babel/standalone@7.8.3/babel.js"></script>
  <!-- important to change this -->
  <script type="text/babel">
    const rootElement = document.getElementById('root')
    const element = <div className="container">Hello World</div>
    ReactDOM.render(element, rootElement)
  </script>
</body>
```

`{}` => means is JS and it evaluates it

How can we pass properties and override one of the properties in this props object?

```js
const rootElement = document.getElementById('root')
const children = 'Hello World'
const className = 'container'
const props = {children, className}
// add new id, spread the props and override className prop
// Last is the winner
const element = <div id="app-root" {...props} className="no-container"/>
ReactDOM.render(element, rootElement)
```

###  Fragment element

It allows two components side by side:

```html
<script type="text/babel">
  const helloElement = React.createElement('span', null, 'Hello')
  const worldElement = React.createElement('span', null, 'World')
  const element = React.createElement(React.Fragment, null, helloElement, worldElement)

  ReactDOM.render(element, document.getElementById('root'))
</script>
```

It could be also done in `jsx`, using the `React.Fragment` element:

```html
<script type="text/babel">
  const element = (
    <React.Fragment>
      <span>Hello</span> <span>World</span>
    </React.Fragment>
  )
  ReactDOM.render(element, document.getElementById('root'))
</script>
```

As `<React.Fragment>` is so comon, there is a especial character for it `<>`. So, we could use:
```html
<script type="text/babel">
  const element = (
    <>
      <span>Hello</span> <span>World</span>
    </>
  )
  ReactDOM.render(element, document.getElementById('root'))
</script>
```

⚠️ 👆 This is the way we should use. Simple. ⚠️ 

### Reusable React Component

```html
<script type="text/babel">
  const Message = ({children}) => {
    return <div className="message">{children}</div>
  }

  const element = (
    <div className="container">
      <Message>Hello World</Message>
      <Message>Goodbye World</Message>
      <Message children="Hola" /> //equivalent but not the best
    </div>
  )

  ReactDOM.render(element, document.getElementById('root'))
</script>
```

### Props types validations:

- Package: `propTypes` that include comon validations.
- In production prop-types are not applied.
- To remove the prop-types from your source code for production: `babel-plugin-transform-react-remove-prop-types`

```js
SayHello.propTypes = {
  firstName: PropTypes.string.isRequired,
  lastName: PropTypes.string.isRequired,
}
```

### Interpolation in JSX

```js
return (
  // jsx
  <div>
    <form>
      <label htmlFor="name">Name: </label>
      <input value={name} onChange={handleChange} id="name" />
    </form>
    { /* js */ name ? <strong>Hello {name}</strong> : 'Please type your name'}
  </div>
)
```

⚠️ `js` blocks in `jsx` can only be expressions. `If`, `loops` or `functions` are not allowed.

### Style (css)

- css elements are inside `{}`, with `camelCase`


### Event handlers & useState

```js
function Greeting() {
  // useState [variable, setVariable function]
  const [name, setName] = React.useState('') // initial value
  // event handler
  const handleChange = event => setName(event.target.value)
  return (
    <div>
      <form>
        <label htmlFor="name">Name: </label>
        // link component with enven handler
        <input onChange={handleChange} id="name" />
      </form>
      {name ? <strong>Hello {name}</strong> : 'Please type your name'}
    </div>
  )
}
```

### useEffect

This function will be called every time the greeting component is rendered.

```js
function Greeting() {
  const [name, setName] = React.useState(
    // get the default value from locaStorage (or empty string)
    // important for the first render but ignore for the rest
    window.localStorage.getItem('name') || '',
  )

  React.useEffect(() => {
    // set the value to localStorage
    window.localStorage.setItem('name', name)
  })

  const handleChange = event => setName(event.target.value)

  return (
    <div>
      <form>
        <label htmlFor="name">Name: </label>
        // value propertie displays the value in the input field
        <input value={name} onChange={handleChange} id="name" />
      </form>
      {name ? <strong>Hello {name}</strong> : 'Please type your name'}
    </div>
  )
}
```

### Lazy initializer with useState

It means: useState allows provide a function as the initial value. That function will be called to retrieve the initial value. That function will only be called when it's absolutely necessary to retrieve the initial value. 

```js
function Greeting() {
  const [name, setName] = React.useState(
    // synchronous function only called the first render
    // it does the same as the previous example but avoid multiple calls to localStorage
    () => window.localStorage.getItem('name') || '',
  )

  React.useEffect(() => {
    window.localStorage.setItem('name', name)
  })

  const handleChange = event => setName(event.target.value)

  return (
    <div>
      <form>
        <label htmlFor="name">Name: </label>
        <input value={name} onChange={handleChange} id="name" />
      </form>
      {name ? <strong>Hello {name}</strong> : 'Please type your name'}
    </div>
  )
}
```

### useEffect

useEffect allows a second argument is a dependency array where you pass all the dependencies for your side effect.
It's important to keep the dependency accurate.

```js
const [name, setName] = React.useState(
  () => window.localStorage.getItem('name') || '',
)

React.useEffect(() => {
  window.localStorage.setItem('name', name)
}, [name]) //only called when name is changed
```

React team has created an ESLint plugin called `eslint-plugin-react-hooks`, which you can use to not only ensure that that dependency array is kept up to date, but actually keep it up to date automatically for you, using ESLint's fix feature.

### Custom hook

```js
// custom hook
// name convention: start with `use`
 function useLocalStorageState(key, defaultValue = '') {
  const [state, setState] = React.useState(
    () => window.localStorage.getItem(key) || defaultValue,
  )

  React.useEffect(() => {
    window.localStorage.setItem(key, state)
  }, [key, state]) //now it has two dependency

  // need to return... similar as useState
  return [state, setState]
}
```

```js
function Greeting() {
  const [name, setName] = useLocalStorageState('name')

  const handleChange = event => setName(event.target.value)

  return (
    <div>
      <form>
        <label htmlFor="name">Name: </label>
        <input value={name} onChange={handleChange} id="name" />
      </form>
      {name ? <strong>Hello {name}</strong> : 'Please type your name'}
    </div>
  )
}
```

Another example of useEffect: destroy element and avoid memory leaks

```js
function Tilt({children}) {
  const tiltRef = React.useRef()

  React.useEffect(() => {
    const tiltNode = tiltRef.current
    const vanillaTiltOptions = {
      max: 25,
      speed: 400,
      glare: true,
      'max-glare': 0.5,
    }
    VanillaTilt.init(tiltNode, vanillaTiltOptions)
    // when the element is removed. It avoid memory leaks
    return () => {
      tiltNode.vanillaTilt.destroy()
    }
  }, []) // [ ] means only do in render and return to destroy it

  return (
    <div ref={tiltRef} className="tilt-root">
      <div className="tilt-child">{children}</div>
    </div>
  )
}
```        

### React Hook Flow

Very good video that explain it: https://egghead.io/lessons/react-understand-the-react-hook-flow

The chart that sum it up:

![image](/assets/images/2020-05-24-A-beginners-guide-to-react/hook-flow.png "React Hook Flow Diagram")
from [https://github.com/donavon/hook-flow](https://github.com/donavon/hook-flow)
<!-- <img src="./hook-flow.png" alt="React Hook Flow Diagram"> -->

### Basic Forms

`event.preventDefault()` => avoid full page refresh

The simple way is using the `id`

```js
function UsernameForm() {
  function handleSubmit(event) {
    // avoid full page refresh
    event.preventDefault()
    // select the value in element usernameInput
    const username = event.target.elements.usernameInput.value
    alert(`You entered: ${username}`)
  }

  return (
    <form onSubmit={handleSubmit}>
      <div>
        // label htmlFor has to match input id. It help accesibility  
        <label htmlFor="usernameInput">Username:</label>
        // add id to identify it's value
        <input id="usernameInput" type="text" />
      </div>
      <button type="submit">Submit</button>
    </form>
  )
}
```

Another solutions: use `useRef`

```js
function UsernameForm() {
  // create a useRef
  const usernameInputref = React.useRef()
  function handleSubmit(event) {
    event.preventDefault()
    // select the current vaalue of useRef
    const username = usernameInputref.current.value
    alert(`You entered: ${username}`)
  }

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label htmlFor="usernameInput">Username:</label>
        // add ref to the input
        <input ref={usernameInputref} id="usernameInput" type="text" />
      </div>
      <button type="submit">Submit</button>
    </form>
  )
}
```

### Dynamic forms

it uses:
- `useState`
- `onChange` has a `handleChange` function
- error displayed
- disable button if there is an error.

```js
function UsernameForm() {
  // to username
  const [username, setUsername] = React.useState('')
  // check if condition match
  const isLowerCase = username === username.toLowerCase()
  // error
  const error = isLowerCase ? null : 'Username must be lower case'

  function handleSubmit(event) {
    event.preventDefault()
    // using the onChange and setUsername in the handler
    // makes the username is always update
    // we can use it
    alert(`You entered: ${username}`)
  }

  function handleChange(event) {
    setUsername(event.target.value)
  }

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label htmlFor="usernameInput">Username:</label>
        // onChange to check the input value directly
        <input id="usernameInput" type="text" onChange={handleChange} />
      </div>
      // error block
      <div style={{color: 'red'}}>{error}</div>
      // disable the button if error
      <button disabled={Boolean(error)} type="submit">
        Submit
      </button>
    </form>
  )
}
```

### Controlling Form Values

Use the `value` inside a `input`

```js
<input
  id="usernameInput"
  type="text"
  onChange={handleChange}
  // React no longer needs to manage the state of this input
  // We control it's input value
  value={username}
/>
```

### Eror Boundaries

Simple way is to use the 3rd party library: [React-error-boundary](https://github.com/bvaughn/react-error-boundary#readme)

```html
<script type="text/babel">
  const ErrorBoundary = ReactErrorBoundary.ErrorBoundary

  // this is a simplify version of what ErrorBoundary does
  // class ErrorBoundary extends React.Component {
  //   state = {error: null}
  //   static getDerivedStateFromError(error) {
  //     return {error}
  //   }
  //   render() {
  //     const {error} = this.state
  //     if (error) {
  //       return <this.props.FallbackComponent error={error} />
  //     }

  //     return this.props.children
  //   }
  // }

  // What replaced the code when there is an error
  function ErrorFallback({error}) {
    return (
      <div>
        <p>Something went wrong:</p>
        <pre>{error.message}</pre>
      </div>
    )
  }

  function Bomb() {
    throw new Error('💥 CABOOM 💥')
  }

  function App() {
    const [explode, setExplode] = React.useState(false)
    return (
      <div>
        <div>
          <button onClick={() => setExplode(true)}>💣</button>
        </div>
        <div>
          // Use ErrorBoundary with fallback component the error fallback function
          <ErrorBoundary FallbackComponent={ErrorFallback}>
            {explode ? <Bomb /> : 'Push the button Max!'}
          </ErrorBoundary>
        </div>
      </div>
    )
  }
  ReactDOM.render(<App />, document.getElementById('root'))
</script>
```

We should use several `<ErrorBoundary FallbackComponent={ErrorFallback}>` with different `FallbackComponent` for different errors could happen in the code.

### Rendering a List

⚠️ Each `li` component needs a unique `key`. Not use the index. It has to be something unique.

Having 

```js
items = [
  {id: 'a', value: 'apple'},
  {id: 'o', value: 'orange'},
  {id: 'g', value: 'grape'},
  {id: 'p', value: 'pear'},
]
```

```js
<ul style={{listStyle: 'none', paddingLeft: 0}}>
  {items.map(item => (
    // each li needs an unique key
    <li key={item.id}>
      <button onClick={() => removeItem(item)}>remove</button>{' '}
      <label htmlFor={`${item.value}-input`}>{item.value}</label>{' '}
      <input id={`${item.value}-input`} defaultValue={item.value} />
    </li>
  ))}
</ul>
```

### Lifting State

```js
<script type="text/babel">
  function Name({ name, onNameChange }) {
    return (
      <div>
        <label>Name: </label>
        <input value={name} onChange={onNameChange} />
      </div>
    );
  }

  // pass animal and function to detect it's change
  function FavoriteAnimal({ animal, onAnimalChange }) {
    return (
      <div>
        <label>Favorite Animal: </label>
        <input
          value={animal}
          onChange={onAnimalChange}
        />
      </div>
    );
  }

  function Display({ name, animal }) {
    return <div>{`Hey ${name}, you are great! Your animal fav is ${animal}`}</div>;
  }

  function App() {
    const [name, setName] = React.useState("");
    // use animal and setAnimal at this level as it need to be accesible by Display and FavoriteAnimal
    const [animal, setAnimal] = React.useState("");
    return (
      <form>
        <Name
          name={name}
          onNameChange={(event) => setName(event.target.value)}
        />
        <FavoriteAnimal
          animal={animal}
          onAnimalChange={(event) => setAnimal(event.target.value)}
        />
        <Display name={name} animal={animal} />
      </form>
    );
  }

  ReactDOM.render(<App />, document.getElementById("root"));
</script>
```

### HTTP Requests

Fetch is async, so we need to use it `useEffect`

- Rest fetch:

```
TBD
```

- GraphQL fetch:

```js
function fetchPokemon(name) {
  const pokemonQuery = `
    query ($name: String) {
      pokemon(name: $name) {
        id
        number
        name
        attacks {
          special {
            name
            type
            damage
          }
        }
      }
    }
  `

  return window
    .fetch('https://graphql-pokemon.now.sh', {
      // learn more about this API here: https://graphql-pokemon.now.sh/
      method: 'POST',
      headers: {
        'content-type': 'application/json;charset=UTF-8',
      },
      body: JSON.stringify({
        query: pokemonQuery,
        variables: {name},
      }),
    })
    .then(r => r.json())
    .then(response => response.data.pokemon)
}
```

- HTTP Errors:

A good practice is to add a new state for `status`: `const [status, setStatus] = React.useState('idle')`

And them update the component depending of it's status:
- `idle`: no fetch initialized
- `pending`: fetch pending
- `resolved`: fetch resolved successfully
- `rejected`: error in the fetch

### React DevTools

Tip: in console, type: `$r` and now we'll see the hooks, the props, and the type for that component that we have selected.