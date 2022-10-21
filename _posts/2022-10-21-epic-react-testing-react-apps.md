---
title: EpicReact - Testing React Apps
date: 2022-10-21 08:00:00 +01:00
tags: [react, hooks, epicreact]
description: EpicReact - Testing React Apps
---

- [Basic concepts](#basic-concepts)
- [React Testing Library](#react-testing-library)
- [Improve asserts with `@testing-library/jest-dom`](#improve-asserts-with-testing-libraryjest-dom)
- [Avoid implementation details](#avoid-implementation-details)
  - [`fireEvent` vs `userEvent`](#fireevent-vs-userevent)
- [Form Testing](#form-testing)
- [Mocking HTTP responses](#mocking-http-responses)
    - [Mocking Modules](#mocking-modules)
- [testing with context](#testing-with-context)
- [testing custom hooks](#testing-custom-hooks)
- [Tips](#tips)

When we think about how things are used, we need to consider who the users are:

1. The end user that's interacting with our code (clicking buttons/etc)
2. The developer user that's actually using our code (rendering it, calling our
   functions, etc.)

You'll be using assertions from jest: [https://jestjs.io/docs/en/expect](https://jestjs.io/docs/en/expect)


# Basic concepts

```javascript
const div = document.createElement('div');
document.body.append(div);

// option 1
ReactDOM.render(<Counter />, div)

// option 2
const root = createRoot(div)
act(() => root.render(<Counter />))
// if we do console.log(document.body.innerHTML), it prints the element inside a <div>
```

After each test we should remove the div from the body.

```javascript
div.remove() // at the end of the test
// but what if the test fails? Better do it in a beforeEach test
beforeEach(() => {
  document.body.innerHTML = ''
})
```

if you wanted to fire an event that doesn't have a dedicated method (like mouseover) -> using `button.dispatchEvent`:

```javascript
// define the event
const incrementClickEvent = new MouseEvent('click', {
    bubbles: true,
    cancelable: true,
    button: 0,
  })

// then dispatch it
const [button] = div.querySelector('button')
button.dispatchEvent(incrementClickEvent)
```


# [React Testing Library](https://testing-library.com/react)

It simplifies the process of testing React components. It provides light utility functions on top of the [DOM Testing Library](https://testing-library.com), in a way that encourages better testing practices.

```javascript
import {render, fireEvent, screen} from '@testing-library/react'

test('it works', () => {
  const {container} = render(<Example />)
  // container is the div that your component has been mounted onto.

  const input = container.querySelector('input')
  fireEvent.mouseEnter(input) // fires a mouseEnter event on the input

  screen.debug() // logs the current state of the DOM (with syntax highlighting!)
})
```

Pros:
- lack of `cleaning` functionality. It has an auto-cleanup feature 🎉
- handling of
[React's `act` function](https://reactjs.org/docs/test-utils.html#act)
- the `fireEvent` is wrapped in `act` so we don't need to do it ourselves.
  - There are multiple actions in the `fireEvent` API, like `click`, `change`, `submit`, etc.

Learn more about the act:
 - [Fix the "not wrapped in act(...)" warning](https://kentcdodds.com/blog/fix-the-not-wrapped-in-act-warning)

The `act` warning from React is there to tell us that something happened to our component when we weren't expecting anything to happen. So you're supposed to wrap every interaction you make with your component in `act` to let React know that we expect our component to perform some updates.


# Improve asserts with [`@testing-library/jest-dom`](http://testing-library.com/jest-dom)

There are more matchers that you can use to improve your assertions. For example, you can use `toBeVisible` to assert that an element is visible.

See [https://github.com/testing-library/jest-dom](https://github.com/testing-library/jest-dom) for more info.

```javascript
// expect(message.textContent).toBe('Current count: 1')
expect(message).toHaveTextContent('Current count: 1')
```

There are two ways of adding it:
1. add it in the `setupFilesAfterEnv` in the jest.config.js file
2. add it in the `setupTest,js` file


# Avoid implementation details

"Implementation details" is a term referring to how an abstraction accomplishes a certain outcome.

Read more about it:
- [`screen`](https://testing-library.com/docs/dom-testing-library/api-queries#screen)
- [Testing Implementation Details](https://kentcdodds.com/blog/testing-implementation-details)
- [Avoid the Test User](https://kentcdodds.com/blog/avoid-the-test-user)
- [The queries built-into React Testing Library](https://testing-library.com/docs/dom-testing-library/api-queries).


[https://testing-playground.com/](https://testing-playground.com/) to help you how to get the DOM queries right.

## `fireEvent` vs `userEvent`

When a user clicks a button, they first have to move their mouse over the button which will fire some mouse events. They'll also mouse down and mouse up on the input and focus it! Lots of events! That's done with the `userEvent`.

`fireEvent` just fires whatever events you tell it to fire, but `userEvent` fires many other events.

# Form Testing

```javascript
test('submitting the form calls onSubmit with username and password', async () => {
  let submittedData
  const handleSubmit = data => (submittedData = data)
  render(<Login onSubmit={handleSubmit} />)
  const username = 'chucknorris'
  const password = 'i need no password'

  await userEvent.type(screen.getByLabelText(/username/i), username)
  await userEvent.type(screen.getByLabelText(/password/i), password)
  await userEvent.click(screen.getByRole('button', {name: /submit/i}))

  expect(submittedData).toEqual({
    username,
    password,
  })
})
```

Rather than that, we want to use a **mock function** to test that the `onSubmit` function is called with the right data.

```javascript
const handleSubmit = jest.fn()
  render(<Login onSubmit={handleSubmit} />)
  const username = 'chucknorris'
  const password = 'i need no password'

  await userEvent.type(screen.getByLabelText(/username/i), username)
  await userEvent.type(screen.getByLabelText(/password/i), password)
  await userEvent.click(screen.getByRole('button', {name: /submit/i}))

  expect(handleSubmit).toHaveBeenCalledWith({
    username,
    password,
  })
  expect(handleSubmit).toHaveBeenCalledTimes(1)
```

We can always use [faker](https://www.npmjs.com/package/@faker-js/faker) to generate the data for our forms.

```javascript
function buildLoginForm(overrides) {
  return {
    username: faker.internet.userName(),
    password: faker.internet.password(),
    ...overrides,
  }
}
```

Even better, we could use [`@jackfranklin/test-data-bot`](https://www.npmjs.com/package/@jackfranklin/test-data-bot)

```javascript
const buildLoginForm = build({
  fields: {
    username: fake(f => f.internet.userName()),
    password: fake(f => f.internet.password()),
  },
})
```

# Mocking HTTP responses

Use [msw](https://mswjs.io/) to mock HTTP responses.


### Mocking Modules

Sometimes, a module is doing something you don't want to actually do in tests.
Jest makes it relatively simple to mock a module:

```javascript
// math.js
export const add = (a, b) => a + b
export const subtract = (a, b) => a - b

// __tests__/some-test.js
import {add, subtract} from '../math'

jest.mock('../math')

// now all the function exports from the "math.js" module are jest mock functions
// so we can call .mockImplementation(...) on them
// and make assertions like .toHaveBeenCalledTimes(...)
```

Additionally, if you'd like to mock only _parts_ of a module, you can provide
your own "mock module getter" function:

```javascript
jest.mock('../math', () => {
  const actualMath = jest.requireActual('../math')
  return {
    ...actualMath,
    subtract: jest.fn(),
  }
})

// now the `add` export is the normal function,
// but the `subtract` export is a mock function.
```

To learn a bit about how this works, take a look at my repo [how-jest-mocking-works](https://github.com/kentcdodds/how-jest-mocking-works).
It's pretty fascinating.


# testing with context

You can provide a `wrapper` option and
that will ensure that rerenders are wrapped as well:

```javascript
function Wrapper({children}) {
  return <ContextProvider>{children}</ContextProvider>
}

const {rerender} = render(<ComponentToTest />, {wrapper: Wrapper})

rerender(<ComponentToTest newProp={true} />)
```

📜 [https://testing-library.com/docs/react-testing-library/api#wrapper](https://testing-library.com/docs/react-testing-library/api#wrapper)


If we create a function for the Wrapper, we can pass params to it:

```javascript
import {render, screen} from '@testing-library/react'
import {ThemeProvider} from '../../components/theme'
import EasyButton from '../../components/easy-button'

function renderWithProviders({theme = 'light'} = {}) {
  const Wrapper = ({children}) => (
    <ThemeProvider initialTheme={theme}>{children}</ThemeProvider>
  )
  return render({wrapper: Wrapper})
}

// Doing the wrapper more general:
// function renderWithProviders(ui, {theme = 'light', ...options} = {}) {
//   const Wrapper = ({children}) => (
//     <ThemeProvider initialTheme={theme}>{children}</ThemeProvider>
//   )
//   return render(ui, {wrapper: Wrapper, ...options})
// }

test('renders with the light styles for the light theme', () => {
  renderWithProviders(<EasyButton>Easy</EasyButton>)
  const button = screen.getByRole('button', {name: /easy/i})
  expect(button).toHaveStyle(`
    background-color: white;
    color: black;
  `)
})

test('renders with the dark styles for the dark theme', () => {
  renderWithProviders(<EasyButton>Easy</EasyButton>, {
    theme: 'dark',
  })
  const button = screen.getByRole('button', {name: /easy/i})
  expect(button).toHaveStyle(`
    background-color: black;
    color: white;
  `)
})
```

# testing custom hooks

The easiest and most straightforward way to test a custom hook is to create a component that uses it and then test that component instead.

When the custom hook is complex and it's hard to create a component for testing it, we can create a `TestComponent` that contains the hook and then test the `TestComponent`.

```javascript
// Custom hook
function useCounter({initialCount = 0, step = 1} = {}) {
  const [count, setCount] = React.useState(initialCount)
  const increment = () => setCount(c => c + step)
  const decrement = () => setCount(c => c - step)
  return {count, increment, decrement}
}

test('exposes the count and increment/decrement functions', () => {
  // here we create the TestComponent that contains the hook
  let result
  function TestComponent() {
    result = useCounter()
    return null
  }
  // we renders it
  render(<TestComponent />)
  expect(result.count).toBe(0)
  // we interact with the hook
  // we need to wrap it in act as the component will be re-rendered
  act(() => result.increment())
  expect(result.count).toBe(1)
  act(() => result.decrement())
  expect(result.count).toBe(0)
})
```

Learn more about this approach in: [How to test custom React hooks](https://kentcdodds.com/blog/how-to-test-custom-react-hooks)


The best option is to use the `renderHook` function from
[`@testing-library/react`](https://github.com/testing-library/react-testing-library)

```javascript
// custom hook
function useCounter({initialCount = 0, step = 1} = {}) {
  const [count, setCount] = React.useState(initialCount)
  const increment = () => setCount(c => c + step)
  const decrement = () => setCount(c => c - step)
  return {count, increment, decrement}
}

test('allows customization of the initial count', () => {
  const {result} = renderHook(useCounter, {initialProps: {initialCount: 3}})
  // Important!! the result is an object with a `current` property
  expect(result.current.count).toBe(3)
})

// another test that use rerender 
test('the step can be changed', () => {
  const {result, rerender} = renderHook(useCounter, {
    initialProps: {step: 3},
  })
  expect(result.current.count).toBe(0)
  act(() => result.current.increment())
  expect(result.current.count).toBe(3)
  rerender({step: 2})
  act(() => result.current.decrement())
  expect(result.current.count).toBe(1)
})
```



----

# Tips

- `screen.debug()` is a great way to see what's going on in your test.
- use `toMatchInlineSnapshot` to make sure that the snapshot is up to date.

```javascript
expect(screen.getByRole('alert').textContent).toMatchInlineSnapshot(
  `"password required"`,
)
```