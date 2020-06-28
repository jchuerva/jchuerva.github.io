---
title: React - useReducer hook
date: 2020-06-28 17:00:00 +01:00
tags: [React, Front-end, js]
description: React - useReducer hook
---

## Index

- [What is the useReducer hook?](#what-is-the-usereducer-hook)
  * [What arguments are passed to useReducer hook?](#what-arguments-are-passed-to-usereducer-hook)
  * [How do you invoke dispatch?](#how-do-you-invoke-dispatch)
- [Example](#example)


## What is the useReducer hook?

The `UseReducer` hook is a special function which enables one use state and other react features without writing ES6 class components. It is a function which can be used instead of `useState` if one is dealing with complicated state logic.


### What arguments are passed to useReducer hook?

` const [state,  dispatch] = useReducer(reducer, initArg, init);`

- **`reducer`**: Is a **function** usually referred to as reducer. reducer takes **two parameters**: `state` and `action`.
    - `state` the current state.
    - `action` is used for specifying different ways state should be updated. reducer function takes the form below and it is usually defined before the component.

    ```js
    function reducer(state, action){
        if( action satisfies some condition){
            // Do something
            // Return new state
        }
        else if (action satisfies another condition) {
          // Do something
          // Return new state
      }else{ 
          // Do something
          // Return new state
      }
    }
    ```  

- **`initArg`**: If the third argument `init` which is optional is omitted, `initArg` is set as the initial state otherwise it is passed to `init`.
- **`init`** (*optional*): This function is invoked with `initArg` passed to it as an argument and its return value is used for setting the initial state.

```js
function init(initArg){
  // Do some expensive computation
  // Return a value which is used for setting initial state
}
```

### How do you invoke dispatch?

```js
 dispatch({action: "add", value: 2})
```

Passing an object enables you to specify an action as well as pass the data you need for setting/updating state.

## Example:

```js
import React, { useReducer } from "react";
import ReactDOM from "react-dom";

// define the reducer:
// state is the current state
// action is an object
function reducer(state, action) {
  switch (action.type) {
    case "add":
      return state + action.value;
    case "subtract":
      return state - action.value;
    default:
      return state;
  }
}

function App(props) {

  const [count, dispatch] = useReducer(reducer, 10);

  function increaseCountHandler(e) {
    dispatch({ type: "add", value: 1 });
  }

  function decreaseCountHandler(e) {
    dispatch({ type: "subtract", value: 1 });
  }

  return (
    <div>
      <p> {count}</p>
      <p>
        <button onClick={increaseCountHandler}> + </button>
      </p>
      <p>
        <button onClick={decreaseCountHandler}> - </button>
      </p>
    </div>
  );
}

const element = document.getElementById('root');
ReactDOM.render(<App />, element);
```

Note:
`state` is the current state and the `action` is an object which is passed to `reducer` using `dispatch`.