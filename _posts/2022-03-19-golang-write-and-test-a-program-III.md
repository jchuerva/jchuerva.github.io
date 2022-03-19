---
title: Go - Write and test a program III - API
date: 2022-03-19 00:40:00 +01:00
tags: [golang]
description: Go - Write and test a program III - API
---

Source: [Write and test a program in Go](https://docs.microsoft.com/en-us/learn/modules/go-write-test-program/)

From [part I]({% post_url 2022-03-19-golang-write-and-test-a-program-I %}) and [part II]({% post_url 2022-03-19-golang-write-and-test-a-program-II %})

- [Write the bank API](#write-the-bank-api)
  - [Set up an account in memory](#set-up-an-account-in-memory)
  - [Expose the statement method (API endpoint)](#expose-the-statement-method-api-endpoint)
  - [Expose the deposit method (API endpoint)](#expose-the-deposit-method-api-endpoint)
  - [Expose the withdraw method (API endpoint)](#expose-the-withdraw-method-api-endpoint)

# Write the bank API

For now, we won't use a database to persist data, so we'll have to create a global variable to store all the accounts in memory

## Set up an account in memory

In `bankapi/main.go`, we'll create the global variable `accounts` to store all the accounts in memory. We will add a first account on it.

```golang
package main

import (
    "github.com/msft/bank"
)

// map of accounts
// key: number
// value: direction of the account
var accounts = map[float64]*bank.Account{}

func main() {
    accounts[1001] = &bank.Account{
        Customer: bank.Customer{
            Name:    "John",
            Address: "Los Angeles, California",
            Phone:   "(213) 555 0147",
        },
        Number: 1001,
    }
}
```

## Expose the statement method (API endpoint)

We need the `net/http` package for it. We also need the `HandleFunc` and `ListenAndserve` method from the `http` package.

In `bankapi/main.go`, we'll create the `statement` method.

```golang
func statement(w http.ResponseWriter, req *http.Request) {
    // get the account number from the URL
    numberqs := req.URL.Query().Get("number")

    if numberqs == "" {
        fmt.Fprintf(w, "Account number is missing!")
        return
    }

    if number, err := strconv.ParseFloat(numberqs, 64); err != nil {
        fmt.Fprintf(w, "Invalid account number!")
    } else {
        account, ok := accounts[number]
        if !ok {
            fmt.Fprintf(w, "Account with number %v can't be found!", number)
        } else {
            fmt.Fprintf(w, account.Statement())
        }
    }
}
```

Let's add the `statement` method in the `main()`:
```golang
func main() {
    accounts[1001] = &bank.Account{
        Customer: bank.Customer{
            Name:    "John",
            Address: "Los Angeles, California",
            Phone:   "(213) 555 0147",
        },
        Number: 1001,
    }


    http.HandleFunc("/statement", statement) //handle GET requests like http://localhost:8000/statement?number=1001
    log.Fatal(http.ListenAndServe("localhost:8000", nil))
}
```

For testing it, we just can to open a browser and go to `http://localhost:8000/statement?number=1001` or `curl http://localhost:8000/statement\?number=1001`

## Expose the deposit method (API endpoint)

```golang
func deposit(w http.ResponseWriter, req *http.Request) {
    numberqs := req.URL.Query().Get("number")
    amountqs := req.URL.Query().Get("amount")

    // This a very common check... we should extract this, right?
    if numberqs == "" {
        fmt.Fprintf(w, "Account number is missing!")
        return
    }

    if number, err := strconv.ParseFloat(numberqs, 64); err != nil {
        fmt.Fprintf(w, "Invalid account number!")
    } else if amount, err := strconv.ParseFloat(amountqs, 64); err != nil {
        fmt.Fprintf(w, "Invalid amount number!")
    } else {
        account, ok := accounts[number]
        if !ok {
            fmt.Fprintf(w, "Account with number %v can't be found!", number)
        } else {
            err := account.Deposit(amount)
            if err != nil {
                fmt.Fprintf(w, "%v", err)
            } else {
                fmt.Fprintf(w, account.Statement())
            }
        }
    }
}
```

Then, we update the `main()`:
```golang
//...
  http.HandleFunc("/statement", statement)
  http.HandleFunc("/deposit", deposit)
  log.Fatal(http.ListenAndServe("localhost:8000", nil))
```

To test it: `http://localhost:8000/deposit?number=1001&amount=100` in url or `curl http://localhost:8000/deposit\?number=1001&amount=100`

## Expose the withdraw method (API endpoint)

```golang
func withdraw(w http.ResponseWriter, req *http.Request) {
    numberqs := req.URL.Query().Get("number")
    amountqs := req.URL.Query().Get("amount")

    if numberqs == "" {
        fmt.Fprintf(w, "Account number is missing!")
        return
    }

    if number, err := strconv.ParseFloat(numberqs, 64); err != nil {
        fmt.Fprintf(w, "Invalid account number!")
    } else if amount, err := strconv.ParseFloat(amountqs, 64); err != nil {
        fmt.Fprintf(w, "Invalid amount number!")
    } else {
        account, ok := accounts[number]
        if !ok {
            fmt.Fprintf(w, "Account with number %v can't be found!", number)
        } else {
            err := account.Withdraw(amount)
            if err != nil {
                fmt.Fprintf(w, "%v", err)
            } else {
                fmt.Fprintf(w, account.Statement())
            }
        }
    }
}
```

Add that into the `main()` method

Then, test it with `http://localhost:8000/withdraw?number=1001&amount=100` or `curl http://localhost:8000/withdraw\?number=1001&amount=100`

----
----
----
Next: [part IV]({% post_url 2022-03-19-golang-write-and-test-a-program-IV %})