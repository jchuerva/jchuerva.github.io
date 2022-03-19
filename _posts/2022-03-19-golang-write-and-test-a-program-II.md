---
title: Go - Write and test a program II - CLI program
date: 2022-03-19 00:30:00 +01:00
tags: [golang]
description: Go - Write and test a program II - CLI program
---

Source: [Write and test a program in Go](https://docs.microsoft.com/en-us/learn/modules/go-write-test-program/)

From [part I]({% post_url 2022-03-19-golang-write-and-test-a-program-I %})

- [CLI program](#cli-program)
  - [Add the structures for Customer and Account](#add-the-structures-for-customer-and-account)
  - [Implement the deposit method](#implement-the-deposit-method)
  - [Implement the withdraw method](#implement-the-withdraw-method)
  - [Implement the statement method](#implement-the-statement-method)

# CLI program
## Add the structures for Customer and Account

Create the structures for customers and accounts in `bankcore/bank.go`

```golang
package bank

// Customer ...
type Customer struct {
    Name    string
    Address string
    Phone   string
}

// Account ...
type Account struct {
    Customer
    Number  int32
    Balance float64
}
```

Now, let's add that validation in the tests, in `bankcore/bank_test.go`

```golang
package bank

import "testing"

func TestAccount(t *testing.T) {
    account := Account{
        Customer: Customer{
            Name:    "John",
            Address: "Los Angeles, California",
            Phone:   "(213) 555 0147",
        },
        Number:  1001,
        Balance: 0,
    }

    if account.Name == "" {
        t.Error("can't create an Account object")
    }
}
```

## Implement the deposit method

In `bankcore/bank_test.go` add a couple of tests for testing the  `Deposit` method.

```golang
// Test the deposit is updated properly
func TestDeposit(t *testing.T) {
    account := Account{
        Customer: Customer{
            Name:    "John",
            Address: "Los Angeles, California",
            Phone:   "(213) 555 0147",
        },
        Number:  1001,
        Balance: 0,
    }

    account.Deposit(10)

    if account.Balance != 10 {
        t.Error("balance is not being updated after a deposit")
    }
}

// Test the deposit is a negative amount
func TestDepositInvalid(t *testing.T) {
    account := Account{
        Customer: Customer{
            Name:    "John",
            Address: "Los Angeles, California",
            Phone:   "(213) 555 0147",
        },
        Number:  1001,
        Balance: 0,
    }

    if err := account.Deposit(-10); err == nil {
        t.Error("only positive numbers should be allowed to deposit")
    }
}
```

Now, let's add the `Deposit` method in `bankcore/bank.go`:

```golang
func (a *Account) Deposit(amount float64) error {
    if amount <= 0 {
        return errors.New("the amount to deposit should be greater than zero")
    }

    a.Balance += amount
    return nil
}
```

## Implement the withdraw method

Same,  let's add some tests in `bankcore/bank_test.go`:

```golang
// Test the withdraw is updated properly
func TestWithdraw(t *testing.T) {
    account := Account{
        Customer: Customer{
            Name:    "John",
            Address: "Los Angeles, California",
            Phone:   "(213) 555 0147",
        },
        Number:  1001,
        Balance: 0,
    }

    account.Deposit(10) // Personally I don't like depend on the Deposit method...
    account.Withdraw(10)

    if account.Balance != 0 {
        t.Error("balance is not being updated after withdraw")
    }
}
```

Then, let's implement the `Withdraw` method in `bankcore/bank.go`:
```golang
// Withdraw ...
func (a *Account) Withdraw(amount float64) error {
    if amount <= 0 {
        return errors.New("the amount to withdraw should be greater than zero")
    }

    if a.Balance < amount {
        return errors.New("the amount to withdraw should be greater than the account's balance")
    }

    a.Balance -= amount
    return nil
}
```

We should add more tests in the `bankcore/bank_test.go` file for testing the `Withdraw` method.

## Implement the statement method

Test in `bankcore/bank_test.go` the `Statement` method:

```golang
func TestStatement(t *testing.T) {
    account := Account{
        Customer: Customer{
            Name:    "John",
            Address: "Los Angeles, California",
            Phone:   "(213) 555 0147",
        },
        Number:  1001,
        Balance: 0,
    }

    account.Deposit(100)
    statement := account.Statement()
    if statement != "1001 - John - 100" {
        t.Error("statement doesn't have the proper format")
    }
}
```

Then, the code for the `Statement` method in `bankcore/bank.go`:
```golang
// Statement ...
func (a *Account) Statement() string {
    return fmt.Sprintf("%v - %v - %v", a.Number, a.Name, a.Balance)
}
```

These are the 3 action we need on the CLI 🎉

----
----
----
Next: [part III]({% post_url 2022-03-19-golang-write-and-test-a-program-III %})
