---
title: Go - Understand how to use packages, variables, and functions in Go (II) - packages
date: 2021-11-19 13:030:00 +01:00
tags: [golang]
description: Go - Understand how to use packages, variables, and functions in Go (II) - pacakges
---

- [Main package](#main-package)
- [Package name convention](#package-name-convention)
- [Create a package](#create-a-package)
- [Create a module](#create-a-module)
- [Reference a local package (a module)](#reference-a-local-package-a-module)
- [Publish a package](#publish-a-package)

Source: [https://docs.microsoft.com/en-us/learn/paths/go-first-steps/](https://docs.microsoft.com/en-us/learn/paths/go-first-steps/)


Packages in Go are like libraries or modules in other programming languages

# Main package

All Go program has to be part of a package. That's why the default package is the `main`. If a program is part of the `main` package, Go generates a binary file. When that file runs, it calls the `main()` function. But when a program is part of a package other than `main`, Go doesn't generate a binary file

- `main` package -> generate a binary file.
- other package -> generate a package archive file (`.a` extension)

# Package name convention

A package uses the last part of its import path as its name.
E.g: the package called `math/cmplx`

```go
// to import it
import "math/cmplx"

// to refer the objects in the package
cmplx.Inf()
```
# Create a package
Create a new directory in the `$GOPATH/src` directory called `calculator`. Create a file called `sum.go`.

```go
// file directory
src/
  calculator/
    sum.go
```

Then, in the `sum.go` file initialize the name of the package and add the code.
```go
// initialize the package
package calculator

// add the code
var logMessage = "[LOG]"

// Version of the calculator
var Version = "1.0"

func internalSum(number int) int {
    return number - 1
}

// Sum two integer numbers
func Sum(number1, number2 int) int {
    return number1 + number2
}
```
To confirm that everything is working, you can run the `go build` command in the calculator directory. No executable is generated. 👍

# Create a module

Go modules typically contain packages that offer related functionality. Go Module = multiple go packages.
Modules also have a versions, and doesn't need to be inside the `$GOPATH/src` directory.

Run this inside the `$GOPATH/src/calculator` directory
```go
go mod init github.com/myuser/calculator

// github.com/myuser/calculator becomes the module's name 
```

The comand creates a `go.mod` file inside the `calculator/` folder, that contains:
```go
module github.com/myuser/calculator

go 1.14
```

# Reference a local package (a module)

Working in a `main` package:

```
src/
  calculator/
    go.mod
    sum.go
  helloworld/
    main.go
```

The `main.go` want to use the `calculator` package:
```go
package main

import (
  "fmt"
  // reference the module
  "github.com/myuser/calculator"
)

func main() {
    total := calculator.Sum(3, 5)
    fmt.Println(total)
    fmt.Println("Version: ", calculator.Version)
}
```

It doesn't work as the package is local, so you need to tell the `main.go` where about the `calculator` package location. For that:
- Init the helloworld package

```
> go mod init helloworld
```

- Update the helloworld.go file to include where the calculator package location

```go
module helloworld

go 1.14

require github.com/myuser/calculator v0.0.0

// refers to your local module
replace github.com/myuser/calculator => ../calculator
```

Now you can run the program: `go run main.go`


# Publish a package

Offical page: [PackagePublishing](https://github.com/golang/go/wiki/PackagePublishing)

We want to publish the `calculator` package, so we need to publish to GitHub. Then add the tag:
```bash
git tag v0.1.0
git push origin v0.1.0
```

Then, the developers want to use this package, should use:
```go
import "github.com/myuser/calculator"
```
