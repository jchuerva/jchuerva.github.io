---
title: Go - Control flows - If/else
date: 2022-01-12 09:00:00 +01:00
tags: [golang]
description: Go - Control flows - If/else
---

Source: [Use control flows in Go](https://docs.microsoft.com/en-us/learn/modules/go-control-flow/)

- [Syntax](#syntax)
  - [Compound if statements](#compound-if-statements)

# Syntax

- The `else` clause is optional, but the `if` braces are still required
- Go doesn't offert support for ternary if statements

```golang
if x%2 == 0 {
    fmt.Println(x, "is even")
}
```

If we want to add the `else` clause:
```golang
if x%2 == 0 {
    fmt.Println(x, "is even")
} else {
    fmt.Println(x, "is odd")
}
```

## Compound if statements

```golang
func givemeanumber() int {
    return -1
}

func main() {
    if num := givemeanumber(); num < 0 {
        fmt.Println(num, "is negative")
    } else if num < 10 {
        fmt.Println(num, "has only one digit")
    } else {
        fmt.Println(num, "has multiple digits")
    }
}
```

The `num` variable stores the value returned by the `givemeanumber()` function, and the variable is available in all `if` branches. The value of `num` is only available inside the `if` statement.