---
title: Go - Use methods and interfaces in Go (I)
date: 2021-11-29 19:00:00 +01:00
tags: [golang]
description: Go - Use methods and interfaces in Go (I)
---

Source: [Use methods and interfaces in Go](https://docs.microsoft.com/en-us/learn/modules/go-methods-interfaces/)

- [Methods in Go](#methods-in-go)
  - [Declare methods](#declare-methods)
  - [Pointers in methods](#pointers-in-methods)
  - [Declare methods for other types](#declare-methods-for-other-types)
  - [Embed methods](#embed-methods)
  - [Overload methods](#overload-methods)
  - [Encapsulation in methods](#encapsulation-in-methods)

# Methods in Go

A method in Go is a special type of function with a simple difference: **you have to include an extra parameter before the function name**. This additional parameter is known as the **receiver**

## Declare methods

```go
func (variable type) MethodName(parameters ...) {
    // method functionality
}
```
Before you can declare a method, you have to create a struct

```go
type triangle struct {
    size int
}

// method for the triangle struct
func (t triangle) perimeter() int {
    return t.size * 3
}

t := triangle{3}
fmt.Println("Perimeter:", t.perimeter()) // Perimeter: 9
```

## Pointers in methods

If you need to update a variable or, if the argument is too big, you might want to avoid copying. In these cases *use pointers to pass the address of a variable.*

```go
func (t *triangle) doubleSize() {
    t.size *= 2
}
t := triangle{3}
fmt.Println("Size:", t.size) // Size: 3
t.doubleSize()
fmt.Println("Size:", t.size) // Size: 6
```

## Declare methods for other types

You can't define a struct from a type that belongs to another package. Therefore, you can't create a method on a basic type, such as a string.
Nonetheless, you can use a **hack to create a custom type from a basic type and then use it as if it were the basic type.**

```go
type upperstring string

func (s upperstring) Upper() string {
    return strings.ToUpper(string(s))
}

s := upperstring("Learning Go!")
fmt.Println(s) // Learning Go!
fmt.Println(s.Upper()) // LEARNING GO!
```

## Embed methods
You can call methods of the embedded struct even if the receiver is different.

```go
type coloredTriangle struct {
    triangle // embed struct
    color string
}

t := coloredTriangle{triangle{3}, "blue"}
mt.Println("Size:", t.size) // Size: 3
```

## Overload methods
You're allowed to have a method with the same name as long as it's specific to the receiver you want to use

```go
// we overlap the perimeter method defined for triangle struct
// now we define it for the coloredTriangle receiver
func (t coloredTriangle) perimeter() int {
    return t.size * 3 * 2
}

t := coloredTriangle{triangle{3}, "blue"}
fmt.Println("Size:", t.size)
fmt.Println("Perimeter (colored)", t.perimeter()) // Perimeter (colored) 18
// We can still access to the triangle perimeter method
fmt.Println("Perimeter (normal)", t.triangle.perimeter()) // Perimeter (normal) 9
```

## Encapsulation in methods

In Go, you need to use only a capitalized identifier to make a method public and an uncapitalized identifier to make a method private.
- **public: capitalized**
- **private: uncapitalized**

```go
package geometry

type Triangle struct {
    size int
}

// private method
func (t *Triangle) doubleSize() {
    t.size *= 2
}

// public method
func (t *Triangle) SetSize(size int) {
    t.size = size
}
```
