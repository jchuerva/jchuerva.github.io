---
title: Go - Error handling
date: 2022-03-21 13:30:00 +01:00
tags: [golang]
description: Go - Error handling
---

Source: [Implement error handling and logging in Go](https://docs.microsoft.com/en-us/learn/modules/go-errors-logs/)

- [Intro](#intro)
- [Error handling strategies](#error-handling-strategies)
  - [Propagate the error in a subroutine](#propagate-the-error-in-a-subroutine)
  - [Run retry logic](#run-retry-logic)
- [Create reusable errors](#create-reusable-errors)

# Intro

In Go, a function that could fail should always return an additional value so that you can anticipate and manage a failure successfully.

Go has built-in functions like `panic` and `recover` to manage exceptions, or unexpected behavior, in your programs. *But errors are known failures that your programs should be built to handle*

```golang
employee, err := getInformation(1000)
if err != nil {
    // Something is wrong. Do something.
}
```

If the error is `nil`, that means success. If it's not `nil`, that means failure.  A `non-nil` error comes with an error message that you can either print or, preferably, *log*.

# Error handling strategies

## Propagate the error in a subroutine

Return the error to the caller without doing anything else:

```golang
func getInformation(id int) (*Employee, error) {
    employee, err := apiCallEmployee(1000)
    if err != nil {
        return nil, err // Simply return the error to the caller.
    }
    return employee, nil
}
```

If we want to include more information in the error message, we can use the `fmt.Errorf` function:

```golang
  if err != nil {
      return nil, fmt.Errorf("Got an error when getting the employee information: %v", err)
  }
```

## Run retry logic

```golang
func getInformation(id int) (*Employee, error) {
    for tries := 0; tries < 3; tries++ {
        employee, err := apiCallEmployee(1000)
        if err == nil {
            return employee, nil
        }

        fmt.Println("Server is not responding, retrying ...")
        time.Sleep(time.Second * 2)
    }

    return nil, fmt.Errorf("server has failed to respond to get the employee information")
}
```

# Create reusable errors

Use the `errors.New()` function to create errors and reuse them in several parts.

```golang
var ErrNotFound = errors.New("Employee not found!")

func getInformation(id int) (*Employee, error) {
    if id != 1001 {
        return nil, ErrNotFound
    }

    ...
}
```

**Good practice:** Err prefix for error variables.

The `errors.Is()` function allows you to compare the type of error.

```golang
if errors.Is(err, ErrNotFound) {
    fmt.Printf("NOT FOUND: %v\n", err)
} else {
    fmt.Print(employee)
}
```

