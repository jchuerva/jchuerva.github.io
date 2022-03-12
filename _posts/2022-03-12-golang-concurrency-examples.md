---
title: Go - Concurrency - Examples
date: 2022-03-12 18:40:00 +01:00
tags: [golang]
description: Go - Concurrency - Examples
---

Source: [Learn how concurrency works in Go](https://docs.microsoft.com/en-us/learn/modules/go-concurrency/)

# Fibonacci numbers

Without concurrency:

```golang
package main

import (
    "fmt"
    "math/rand"
    "time"
)

func fib(number float64) float64 {
    x, y := 1.0, 1.0
    for i := 0; i < int(number); i++ {
        x, y = y, x+y
    }

    r := rand.Intn(3)
    time.Sleep(time.Duration(r) * time.Second)

    return x
}

func main() {
    start := time.Now()

    for i := 1; i < 15; i++ {
        n := fib(float64(i))
    fmt.Printf("Fib(%v): %v\n", i, n)
    }

    elapsed := time.Since(start)
    fmt.Printf("Done! It took %v seconds!\n", elapsed.Seconds())
}
```

Using concurrency:

```golang
package main

import (
	"fmt"
	"math/rand"
	"time"
)

func fib(number float64, ch chan string) {
	x, y := 1.0, 1.0
	for i := 0; i < int(number); i++ {
		x, y = y, x+y
	}

	r := rand.Intn(3)
	time.Sleep(time.Duration(r) * time.Second)

	ch <- fmt.Sprintf("Fib(%v): %v\n", number, x)
}

func main() {
	start := time.Now()

	size := 15
	ch := make(chan string, size)

	for i := 0; i < size; i++ {
		go fib(float64(i), ch)
	}

	for i := 0; i < size; i++ {
		fmt.Printf(<-ch)
	}

	elapsed := time.Since(start)
	fmt.Printf("Done! It took %v seconds!\n", elapsed.Seconds())
}
```