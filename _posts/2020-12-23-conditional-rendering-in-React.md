---
title: Conditional Rendering in React
date: 2020-12-23 19:00:00 +01:00
tags: [React, Front-end, js]
description: Conditional Rendering in React
---

## Index
- [Index](#index)
- [Using an If…else Statement](#using-an-ifelse-statement)
- [Using Element Variables](#using-element-variables)
- [Ternary Operators](#ternary-operators)
- [Logical &&](#logical-)
- [Invoked Function Expressions](#invoked-function-expressions)
- [Source](#source)

## Using an If…else Statement

```js
....
render() {
    let {isLoggedIn} = this.state;
    return (
      <div className="App">
        <h1>
          This is a Demo showing several ways to implement Conditional Rendering
          in React.
        </h1>
        {
          if(isLoggedIn){
            return <button>Logout</button>
          } else{
            return <button>Login</button>
          }
        }
      </div>
    );
}
```

To avoid the *if..else* inside the *render*, we can extract it into a function, but if and else have to return something!

```js
...
render() {
    let {isLoggedIn} = this.state;
    const renderAuthButton = ()=>{
      if(isLoggedIn){
        return <button>Logout</button>
      } else{
        return <button>Login</button>
      }
    }
    return (
      <div className="App">
        <h1>
          This is a Demo showing several ways to implement Conditional Rendering
          in React.
        </h1>
        {renderAuthButton()}
      </div>
    );
  }
...
```

## Using Element Variables

Use the variable to store the jsx element

```js
...
render() {
    let { isLoggedIn } = this.state;
    let AuthButton;
    if (isLoggedIn) {
      AuthButton = <button>Logout</button>;
    } else {
      AuthButton = <button>Login</button>;
    }
    return (
      <div className="App">
        <h1>
          This is a Demo showing several ways to implement Conditional Rendering
          in React.
        </h1>
        {AuthButton}
      </div>
    );
  }
...
```


## Ternary Operators
Simple solution using the ternary operator, but you need and else...

```js
...
render() {
    let { isLoggedIn } = this.state;
    return (
      <div className="App">
        <h1>
          This is a Demo showing several ways to implement Conditional Rendering
          in React.
        </h1>
        {isLoggedIn ? <button>Logout</button> : <button>Login</button>}
      </div>
    );
  }
...
```


## Logical &&
If you don't need an else, this is the best solution

```js
...
return (
      <div className="App">
        <h1>
          This is a Demo showing several ways to implement Conditional Rendering
          in React.
        </h1>
        {isLoggedIn && <button>Logout</button>}
        {!isLoggedIn && <button>Login</button>}
      </div>
    );
  }
...
```

## Invoked Function Expressions
Basically, use a function inside the renturn

```js
...
return (
      <div className="App">
        <h1>
          This is a Demo showing several ways to implement Conditional Rendering
          in React.
        </h1>
        {(()=> {
          if (isLoggedIn) {
            return <button>Logout</button>;
          } else {
            return <button>Login</button>;
          }
        })()}
      </div>
    );
  }
...
```

## Source
[https://www.digitalocean.com/community/tutorials/7-ways-to-implement-conditional-rendering-in-react-applications](https://www.digitalocean.com/community/tutorials/7-ways-to-implement-conditional-rendering-in-react-applications)
