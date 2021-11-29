---
title: Go - Understand how to use packages, variables, and functions in Go (I) - Basic data types
date: 2021-11-18 14:00:00 +01:00
tags: [golang]
description: Go - Understand how to use packages, variables, and functions in Go (I) - Basic data types
---

- [Basic data types](#basic-data-types)
  - [Integer](#integer)
  - [Floating-point numbers](#floating-point-numbers)
  - [Booleans](#booleans)
  - [Strings](#strings)
  - [Default values](#default-values)
- [Type conversions](#type-conversions)
  - [Between numbers:](#between-numbers)
  - [String an int](#string-an-int)

Source: [Understand how to use packages, variables, and functions in Go](https://docs.microsoft.com/en-us/learn/modules/go-variables-functions-packages/)

# Basic data types

## Integer

```go
var integer int = 123

// But there are other integer types
var integer8 int8 = 127
var integer16 int16 = 32767
var integer32 int32 = 2147483647
var integer64 int64 = 9223372036854775807
```

A `rune` is simply an alias for `int32` data type. It's used to represent a Unicode character (or a Unicode code point)

```go
rune := 'G'
fmt.Println(rune) # 71 unicode character for G
```

## Floating-point numbers

```go
var float32 float32 = 2147483647
var float64 float64 = 9223372036854775807
```

## Booleans

```go
var featureFlag bool = true
```

## Strings

To initialize a string variable, you need to define its value within double quotation marks ("). Single quotation marks (') are used for single characters (and for runes, as we saw in a previous section)

```go
var firstName string = "John"
lastName := "Doe"
```

Escape characters:
- \n for new lines
- \r for carriage returns
- \t for tabs
- \' for single quotation marks
- \" for double quotation marks
- \\ for backslashes


E.g:
```go
fullName := "John Doe \t(alias \"Foo\")\n"
fmt.Println(fullName) # John Doe        (alias "Foo")
```

## Default values

All data types have a default value when you don't initialize a variable:

- 0 for int types (and all of its subtypes, like int64)
- +0.000000e+000 for float32 and float64 types
- false for bool types
- An empty value for string types

# Type conversions

## Between numbers:

```go
var integer16 int16 = 127
int32(integer16) # it's a int32
```

## String an int
Use the `strconv` package:
- Atoi (string to int)
- Itoa (int to string)

```go
package main

import (
  "strconv"
)

func main() {
  i, err := strconv.Atoi("-42")
  s := strconv.Itoa(-42)
}
```
