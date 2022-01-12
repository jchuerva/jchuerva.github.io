---
title: Go - Use methods and interfaces in Go (II)
date: 2021-11-30 09:00:00 +01:00
tags: [golang]
description: Go - Use methods and interfaces in Go (II)
---

Source: [Use methods and interfaces in Go](https://docs.microsoft.com/en-us/learn/modules/go-methods-interfaces/)

- [Use interfaces in Go](#use-interfaces-in-go)
  - [Declare an interface](#declare-an-interface)
  - [Implement an interface](#implement-an-interface)
  - [Examples](#examples)
    - [Implement a Stringer interface](#implement-a-stringer-interface)
    - [Extend an existing implementation](#extend-an-existing-implementation)
    - [Write a custom server API](#write-a-custom-server-api)

# Use interfaces in Go

Interfaces in Go are a type of data that's used to represent the behavior of other types. An interface is like a blueprint or a *contract that an object should satisfy*

## Declare an interface

An interface in Go is an abstract type that includes a set of methods.

```go
type Shape interface {
  Perimeter() float64
  Area() float64
}
```

The `Shape` interface means that any type that you want to consider a Shape needs to have both the `Perimeter()` and `Area()` methods. E.g: when you create a Square struct, it has to implement both methods.

## Implement an interface

Go don't have a keyword to implement an interface. An interface in Go is satisfied implicitly by a type when it has all the methods that an interface requires.

```go
type Square struct {
    size float64
}

func (s Square) Area() float64 {
    return s.size * s.size
}

func (s Square) Perimeter() float64 {
    return s.size * 4
}
```

Notice how the method's signature of the Square struct matches the signature of the Shape interface.

Let's create another type, such as Circle:

```go
type Circle struct {
    radius float64
}

func (c Circle) Area() float64 {
    return math.Pi * c.radius * c.radius
}

func (c Circle) Perimeter() float64 {
    return 2 * math.Pi * c.radius
}
```

Both `Square` and `Circle` have the same methods as the `Shape` interface, so we can not define more generic functions:
```go
func printInformation(s Shape) {
    fmt.Printf("%T\n", s)
    fmt.Println("Area: ", s.Area())
    fmt.Println("Perimeter:", s.Perimeter())
    fmt.Println()
}

// In the code we can use it as
var s Shape = Square{3}
printInformation(s) // main.Square, Area:  9, Perimeter: 12

// or without specifying the type
c:= Circle{6}
printInformation(c) // main.Circle, Area:  113.09733552923255, Perimeter: 37.69911184307752
```

The beauty of using interfaces is that, for every new type or implementation of Shape, the printInformation function doesn't have to change.

## Examples

### Implement a Stringer interface

A example of extending existing functionality is to use a `Stringer`, which is an interface that has a `String()` method, like this:

```go
type Stringer interface {
    String() string // String() is the method that will be called when custom print
}
```

Now, we can crete a new `String()` public method for our struct

```go
package main

import "fmt"

type Person struct {
    Name, Country string
}

// we create a custom String() method for our Person struct
func (p Person) String() string {
    return fmt.Sprintf("%v is from %v", p.Name, p.Country)
}
func main() {
    rs := Person{"John Doe", "USA"}
    ab := Person{"Mark Collins", "United Kingdom"}
    fmt.Printf("%s\n%s\n", rs, ab)
    // John Doe is from USA
    // Mark Collins is from United Kingdom
}
```

We use a custom type to write a custom version of the `String()` method.

### Extend an existing implementation

We have this code and we want to extend it by writing a custom implementation of `Write()` method, that's in charge of manipulating some data.

```go
package main

import (
    "fmt"
    "io"
    "net/http"
    "os"
)

func main() {
    resp, err := http.Get("https://api.github.com/users/microsoft/repos?page=15&per_page=5")
    if err != nil {
        fmt.Println("Error:", err)
        os.Exit(1)
    }

    io.Copy(os.Stdout, resp.Body)
}
```

Output example:
```
[{"id":276496384,"node_id":"MDEwOlJlcG9zaXRvcnkyNzY0OTYzODQ=","name":"-Users-deepakdahiya-Desktop-juhibubash-test21zzzzzzzzzzz","full_name":"microsoft/-Users-deepakdahiya-Desktop-juhibubash-test21zzzzzzzzzzz","private":false,"owner":{"login":"microsoft","id":6154722,"node_id":"MDEyOk9yZ2FuaXphdGlvbjYxNTQ3MjI=","avatar_url":"https://avatars2.githubusercontent.com/u/6154722?v=4","gravatar_id":"","url":"https://api.github.com/users/microsoft","html_url":"https://github.com/micro
....
```

We want to modify what's is printed out from `os.Stdout`. That's part of the [io package](https://golang.org/pkg/io/).

Checking the `Copy` method definition `func Copy(dst Writer, src Reader) (written int64, err error)`, meaning we have to check the `dst Writer` parameter. But the `Writer` is a interface:

```go
type Writer interface {
  Write(p []byte) (n int, err error)
}
```

So we can manipulate the `Writer` interface and create a new custom `Write` method for our `os.Stdout`.

First, we need to implement a custom interface, like a empty struct where we apply the new `Write` method:

```go
// empty struct where wer are going to write the interface
type customWriter struct{}

// struct to parse the API response
type GitHubResponse []struct {
    FullName string `json:"full_name"`
}

// new Write method for our customWriter struct
func (w customWriter) Write(p []byte) (n int, err error) {
  // create an empty struct only for the full_name field
  var resp GitHubResponse
  // unmarshal the response into the struct
  json.Unmarshal(p, &resp)
  for _, r := range resp {
      fmt.Println(r.FullName)
  }
  return len(p), nil
}
```

So, using the new interface:
```go
func main() {
  resp, err := http.Get("https://api.github.com/users/microsoft/repos?page=15&per_page=5")
  if err != nil {
      fmt.Println("Error:", err)
      os.Exit(1)
  }

  writer := customWriter{}
  io.Copy(writer, resp.Body)
}
```

### Write a custom server API

The typical way of writing a web server is by using the `http.Handler` interface from the `net/http` package.

```go
package http

type Handler interface {
    ServeHTTP(w ResponseWriter, r *Request)
}

func ListenAndServe(address string, h Handler) error
```
Notice how the `ListenAndServe` function is expecting a server address, such as `http://localhost:8000`, and an instance of the `Handler` that will dispatch the response from the call to the server address.

E.g:
```go
package main

import (
    "fmt"
    "log"
    "net/http"
)

// create a custom type
type dollars float32

// Implement the String for the new custom type
func (d dollars) String() string {
    return fmt.Sprintf("$%.2f", d)
}

// custom type
type database map[string]dollars

// use the database as receiver
// uses the data from the receiver, loops through it and prints out each item
func (db database) ServeHTTP(w http.ResponseWriter, req *http.Request) {
    for item, price := range db {
        fmt.Fprintf(w, "%s: %s\n", item, price)
    }
}

func main() {
    db := database{"Go T-Shirt": 25, "Go Jacket": 55}
    log.Fatal(http.ListenAndServe("localhost:8000", db))
}
```




