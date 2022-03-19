---
title: Go - Write and test a program IIII - Improvements
date: 2022-03-19 00:50:00 +01:00
tags: [golang]
description: Go - Write and test a program IIII - Improvements
---

Source: [Write and test a program in Go](https://docs.microsoft.com/en-us/learn/modules/go-write-test-program/)

# Implement the transfer method

The ability to transfer money to another account.

Let's start adding the test in `bankcore/bank_test.go`

```golang

//...

func TestTransfer(t *testing.T){
  // we need two accounts
  accountA := Account{
		Customer: Customer{
			Name:    "John",
			Address: "Los Angeles, California",
			Phone:   "(213) 555 0147",
		},
		Number:  1001,
		Balance: 0,
	}

	accountB := Account{
		Customer: Customer{
			Name:    "Mark",
			Address: "Irvine, California",
			Phone:   "(949) 555 0198",
		},
		Number:  1002,
		Balance: 0,
	}

  accountA.Deposit(100)
  err := accountA.Transfer(50, &accountB)

  if accountA.Balance != 50 && accountB.Balance != 50 {
		t.Error("transfer from account A to account B is not working", err)
	}
}
```

Then, let's implement the method in `bankcore/bank.go`

```golang
package bank

import (
  "errors"
  "fmt"
)
// ...

// Transfer function
func (a *Account) Transfer(amount float64, dest *Account) error {
  if amount <= 0 {
		return errors.New("the amount to transfer should be greater than zero")
	}

	if a.Balance < amount {
		return errors.New("the amount to transfer should be greater than the account's balance")
	}

	a.Withdraw(amount)
	dest.Deposit(amount)
	return nil
}
```

We have now to write the API endpoint for the transfer method in `bankapi/bank.go`
```golang
func transfer(w http.ResponseWriter, req *http.Request) {
	numberqs := req.URL.Query().Get("number")
	amountqs := req.URL.Query().Get("amount")
	destqs := req.URL.Query().Get("dest")

	if numberqs == "" {
		fmt.Fprintf(w, "Account number is missing!")
		return
	}

	if number, err := strconv.ParseFloat(numberqs, 64); err != nil {
		fmt.Fprintf(w, "Invalid account number!")
	} else if amount, err := strconv.ParseFloat(amountqs, 64); err != nil {
		fmt.Fprintf(w, "Invalid amount number!")
	} else if dest, err := strconv.ParseFloat(destqs, 64); err != nil {
		fmt.Fprintf(w, "Invalid account destination number!")
	} else {
		if accountA, ok := accounts[number]; !ok {
			fmt.Fprintf(w, "Account with number %v can't be found!", number)
		} else if accountB, ok := accounts[dest]; !ok {
			fmt.Fprintf(w, "Account with number %v can't be found!", dest)
		} else {
			err := accountA.Transfer(amount, accountB.Account)
			if err != nil {
				fmt.Fprintf(w, "%v", err)
			} else {
				fmt.Fprintf(w, accountA.Statement())
			}
		}
	}
}
```

# Modify the statement endpoint to return a JSON object

We'd like you to make this change expecting that anyone who uses your core package might want to implement a different statement method to change the output.

We need to:
- Create an interface with a `Statement()` string function
- Create a new `Statement()` function in your core package that receives the interface that you created as a parameter. This function should call the `Statement()` method that your structures already have.

In `bankapi/bank.go`
First we create interface with a `Statement()` string function
```golang
// Bank ...
type Bank interface {
	Statement() string
}
```

We also add the new `Statement()` function in it that calls the `Statement()` method:
```golang
func Statement(b bank) string {
  return b.Statement()
}
```

Now, we need to add the `Statement()` method in the `main.go` file:
```golang
// ...
// Statement ...
func (c *CustomAccount) Statement() string {
  // convert into JSON
	json, err := json.Marshal(c)
	if err != nil {
		return err.Error()
	}

	return string(json)
}

// where we define `CustomAccount` structure as
type CustomAccount struct {
  *bank.Account
}
```



