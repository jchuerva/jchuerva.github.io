---
title: Go - Control flows - Examples
date: 2022-03-09 17:00:00 +01:00
tags: [golang]
description: Go - Control flows - Examples
---

Source: [Use control flows in Go](https://docs.microsoft.com/en-us/learn/modules/go-control-flow/)

# FizzBuzz

First, write a program that prints numbers 1 through 100, with the following changes:
- Print Fizz if the number is divisible by 3.
- Print Buzz if the number is divisible by 5.
- Print FizzBuzz if the number is divisible by both 3 and 5.
- Print the number if none of the previous cases match.
Try to use the switch statement.

```golang
package main

import "fmt"

func fizzbuzz(num int) string {
  switch {
  case num%15 == 0:
    return "FizzBuzz"
  case num%3 == 0:
    return "Fizz"
  case num%5 == 0:
    return "Buzz"
  default:
    return fmt.Sprintf("%d", num)
  }
}

func main() {
	for i := 1; i <= 100; i++ {
    fmt.Println(fizzbuzz(i))
	}
}
```

# Find the primes

Write a program to find all prime numbers less than 20.

```golang
package main

import "fmt"

func findprimes(number int) bool {
	for i := 2; i < number; i ++ {
    if number % i == 0 {
			return false
    }
  }

	if number > 1 {
		return true
	} else {
	  return false
	}
}

func main() {
    fmt.Println("Prime numbers less than 20:")

    for number := 1; number <= 20; number++ {
        if findprimes(number) {
            fmt.Printf("%v ", number)
        }
    }
}
```

# Ask a number, panic if negative

Write a program that asks a user for a number. Use the following code snippet as a starting point:

```golang
package main

import "fmt"

func main() {
    val := 0
    fmt.Print("Enter number: ")
    fmt.Scanf("%d", &val)
    fmt.Println("You entered:", val)
}
```

This program asks for a number and prints it. Modify the example code to:
- Ask continually for an integer number. The exit condition for the loop should be a user entry that's a negative number.
- Crash the program when the user enters a negative number. Then print the stack trace error.
- When the number is 0, print 0 is neither negative nor positive. Keep asking for a number.
- When the number is positive, print You entered: X (where X is the entered number). Keep asking for a number.
- 
For now, ignore the possibility that the user might enter something different than an integer number.

```golang
package main

import "fmt"

func main() {
	val := 0

	for {
		fmt.Print("Enter number: ")
		fmt.Scanf("%d", &val)

		switch {
		case val == 0:
			fmt.Println("0 is neither negative nor positive")
		case val < 0:
			panic("You entered a negative number!")
		default:
			fmt.Println("You entered:", val)
		}

	}
}
```
