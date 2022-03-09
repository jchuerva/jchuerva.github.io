---
title: Go - Control flows - Loop
date: 2022-03-08 09:00:00 +01:00
tags: [golang]
description: Go - Control flows - Loops
---

Source: [Use control flows in Go](https://docs.microsoft.com/en-us/learn/modules/go-control-flow/)

- [Syntax](#syntax)
  - [Empty prestatement and poststatements](#empty-prestatement-and-poststatements)
  - [Infinite loops and break statements](#infinite-loops-and-break-statements)
  - [Continue statements](#continue-statements)

# Syntax

Semicolons (;) separate the three components of for loops:
- An initial statement that's executed before the first iteration (optional).
- A condition expression that's evaluated before every iteration. The loop stops when this condition is false.
- A poststatement that's executed at the end of every iteration (optional).

```golang
func main() {
  sum := 0
  // initial; condition; poststatement
  for i := 1; i <= 100; i++ {
      sum += i
  }
  fmt.Println("sum of 1..100 is", sum)
}
```

## Empty prestatement and poststatements

Go has no `while` keyword. But you can use a for loop instead (without presatement and poststatements).

```golang
for num != 5 {
  num = rand.Int63n(15)
  fmt.Println(num)
}
```

## Infinite loops and break statements

Don't write a condition expression or a prestatement or poststatement. Instead use the `break` statement to exit the loop.

```golang
func main() {
  var num int32
  sec := time.Now().Unix()
  rand.Seed(sec)

  for {
    fmt.Print("Writing inside the loop...")
    if num = rand.Int31n(10); num == 5 {
        fmt.Println("finish!")
        break
    }
    fmt.Println(num)
  }
}
```

## Continue statements

Use the `continue` keyword to skip the current iteration of a loop.

```golang
func main() {
  sum := 0
  for num := 1; num <= 100; num++ {
    if num%5 == 0 {
        continue
    }
    sum += num
  }
  fmt.Println("The sum of 1 to 100, but excluding numbers divisible by 5, is", sum)
}
```


