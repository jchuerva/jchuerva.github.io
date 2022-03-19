---
title: Go - Write and test a program I
date: 2022-03-19 00:20:00 +01:00
tags: [golang]
description: Go - Write and test a program I
---

Source: [Write and test a program in Go](https://docs.microsoft.com/en-us/learn/modules/go-write-test-program/)

- [Project goals](#project-goals)
- [Project structure](#project-structure)
- [Let's add some tests](#lets-add-some-tests)
    - [To run the tests](#to-run-the-tests)

# Project goals

Requirements of the online bank system program:
- Enable customers to create an account.
- Enable customers to withdraw money.
- Enable customers to transfer money to another account.
- Provide an account statement with customer data and a final balance.
- Expose a Web API through an endpoint to print an account statement.

The interaction with the core package will be through a CLI program.

# Project structure

We'll create a Go package for all the bank core logic and a `main` program to initialize the system with a few customers and actions like deposits and transfers. Additionally, this `main` program will start a Web API server to expose an endpoint for the account statement.

```bash
$GOPATH/
  src/
    bankcore/
      go.mod
      bank.go
    bankapi/
      go.mod
      main.go
```

Let's start creating a dummy `bankcore/bank.go` package.

```golang
package bank

func Hello() string {
    return "Hey! I'm working!"
}
```

We have to define the `bankcore/go.mod` file to tell the Go compiler that we're using the `bankcore` package.
```golang
module github.com/msft/bank

go 1.14
```

Regarding the `bankapi`, let's use the `bankcore` local package:

```golang
// src/bankapi/main.go
package main

import (
    "fmt"

    "github.com/msft/bank"
)

func main() {
    fmt.Println(bank.Hello())
}
```

Where in `src/bankapi/go.mod` we indicate about the package we are using:
```golang
module bankapi

go 1.14

require (
    github.com/msft/bank v0.0.1
)

replace github.com/msft/bank => ../bankcore
```

# Let's add some tests

Next to each `XXX.go` file we can add a `XXX_test.go` file with the same name and the `_test` suffix.

```golang
// bankcore/bank_test.go
package bank

import "testing"

func TestAccount(t *testing.T) {

}
```

### To run the tests

```sh
go test -v
```

Run all `*_test.go` files.

-----
-----
-----
Next: [part II]({% post_url 2022-03-19-golang-write-and-test-a-program-II %})