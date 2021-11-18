---
title: Golang (II) - Functions
date: 2021-11-18 14:030:00 +01:00
tags: [golang]
description: Golang (II) - Functions
---

- [Main function](#main-function)
  - [Command-line argumensts](#command-line-argumensts)
- [Custom functions](#custom-functions)
- [Change function parameters values (pointers)](#change-function-parameters-values-pointers)

Source: [https://docs.microsoft.com/en-us/learn/paths/go-first-steps/](https://docs.microsoft.com/en-us/learn/paths/go-first-steps/)

# Main function

All executable programs in Go have this function because it's the program's starting point. You can only have one `main()` function in your program.

If you're creating a *Go package*, you don't need to write a `main()` function.

The `main()` function doesn't have any parameters and returns nothing.


## Command-line argumensts

If you need to access command-line arguments in Go, you can do it with the [`os package`](https://pkg.go.dev/os) and the `os.Args`

```go
package main

import (
    "fmt"
    "os"
    "strconv"
)

func main() {
    // os.Args are string
    number1, _ := strconv.Atoi(os.Args[1])
    number2, _ := strconv.Atoi(os.Args[2])
    fmt.Println("Sum:", number1+number2)
}
```

To execute the program:
```
> go run main.go 3 5
```

# Custom functions

Syntax:
```go
func name(parameters) (results) {
    body-content
}
```

Example:
```go
func calc(number1 string, number2 string) (sum int, mul int) {
    int1, _ := strconv.Atoi(number1)
    int2, _ := strconv.Atoi(number2)
    sum = int1 + int2
    mul = int1 * int2
    return
}
```

If you define the *return variables* in the function, you can simply add a `return` line at the end. *This is not recommended, as it can be unclear what the function is returning.*

You can discard returned values by assigning value to the `_` variable.

# Change function parameters values (pointers)

When you pass a value to a function, every change in that function won't affect the caller. Go takes that value and creates a local copy (new variable in memory).

In Go, there are two operators for working with pointers:
- The `&` operator takes the address of the object that follows it.
- The `*` operator dereferences a pointer. That is, it gives you access to the object at the address contained in the pointer.

```go
package main

import "fmt"

func main() {
    firstName := "John"
    updateName(&firstName) # you are passing the address of the variable
    fmt.Println(firstName)
}

func updateName(name *string) {
    *name = "David" # you are modifiying the content of that variable
}
```


