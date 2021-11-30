---
title: Go - package to manage an online store
date: 2021-11-30 09:00:00 +01:00
tags: [golang]
description: Go - package to manage an online store
---

Source: [Use methods and interfaces in Go](https://docs.microsoft.com/en-us/learn/modules/go-methods-interfaces/)

# Create a package to manage an online store

Write a program that uses a custom package to manage accounts for an online store. Your challenge includes the following four elements:
1. Create a custom type called `Account` that includes the first and last name of the account owner. The type must also include the functionality to `ChangeName`.
2. Create another custom type called `Employee` that includes a variable to store the number of credits as type `float64` and that embeds the `Account` object. The type must also include the functionality to `AddCredits`, `RemoveCredits`, and `CheckCredits`. You need to demonstrate that you can change the account name via the `Employee` object.
3. Write a Stringer method to your `Account` object so that the `Employee` name can be printed out in a format that includes the first and last name.
4. Finally, write a program that consumes the package you created, and test all the functionality listed in this challenge. That is, the main program should change the name, print the name, add credit, remove credit, and check the balance.

-----

# Solution

```go
// new package name
package store

import (
    "errors"
    "fmt"
)

// custom type
type Account struct {
    FirstName string
    LastName  string
}

// custom type
type Employee struct {
    Account
    Credits float64
}

// method for the Account type
// it changes the FirstName of it
func (a *Account) ChangeName(newname string) {
    a.FirstName = newname
}

// method for the Employee type
// it's a custom print
func (e Employee) String() string {
    return fmt.Sprintf("Name: %s %s\nCredits: %.2f\n", e.FirstName, e.LastName, e.Credits)
}

func CreateEmployee(firstName, lastName string, credits float64) (*Employee, error) {
    return &Employee{Account{firstName, lastName}, credits}, nil
}

func (e *Employee) AddCredits(amount float64) (float64, error) {
    if amount > 0.0 {
        e.Credits += amount
        return e.Credits, nil
    }
    return 0.0, errors.New("Invalid credit amount.")
}

func (e *Employee) RemoveCredits(amount float64) (float64, error) {
    if amount > 0.0 {
        if amount <= e.Credits {
            e.Credits -= amount
            return e.Credits, nil
        }
        return 0.0, errors.New("You can't remove more credits than the account has.")
    }
    return 0.0, errors.New("You can't remove negative numbers.")
}

func (e *Employee) CheckCredits() float64 {
    return e.Credits
}
```

Then, the `main` function:

```go
package main

import (
    "fmt"
    "store"
)

func main() {
    bruce, _ := store.CreateEmployee("Bruce", "Lee", 500)
    fmt.Println(bruce.CheckCredits())
    credits, err := bruce.AddCredits(250)
    if err != nil {
        fmt.Println("Error:", err)
    } else {
        fmt.Println("New Credits Balance = ", credits)
    }

    _, err = bruce.RemoveCredits(2500)
    if err != nil {
        fmt.Println("Can't withdraw or overdrawn!", err)
    }

    bruce.ChangeName("Mark")

    fmt.Println(bruce)
}
```



