---
title: Go - Concurrency - Goroutines
date: 2022-03-11 08:00:00 +01:00
tags: [golang]
description: Go - Concurrency - Goroutines
---

Source: [Learn how concurrency works in Go](https://docs.microsoft.com/en-us/learn/modules/go-concurrency/)

- [Concurrency](#concurrency)
  - [Go's approach](#gos-approach)
- [Goroutines](#goroutines)
  - [Write a current program](#write-a-current-program)

# Concurrency

Concurrency is the composition of independent activities, e.g: deal with multiple user requests simultaneously or processing data in batches.

Go has two styles for writing programs:
  - traditional: using threads
  - Go approach: values as passed between independent activities (goroutines) to communicate processes

## Go's approach

In the traditional approach, the biggest problem is sharing data between processes. Go takes a different approach, passing data back and forth through channels. Channels are a way to communicate between activities (goroutines). This means that **only one activity (goroutine) has access to the data, and there's no race condition by design**.

> [*"Do not communicate by sharing memory; instead, share memory by communicating."*](https://go.dev/blog/codelab-share#:%7E:text=Hoare%27s%20Communicating%20Sequential%20Processes?azure-portal=true.)

# Goroutines

A goroutine is a concurrent activity in a lightweight thread (not the traditional one you have in an operating system).

If you want to create another goroutine, you have to use the `go` keyword before calling the function.

```golang
func main(){
    login()
    go launch() // goroutine
}
```

or using an anonymous function:

```golang
func main(){
    login()
    go func() { // goroutine in an anonymous function
        launch()
    }()
}
```

## Write a current program

We have a program that checks if an API endpoint is responding or not.

```golang
package main

import (
    "fmt"
    "net/http"
    "time"
)

func main() {
    start := time.Now()

    apis := []string{
        "https://management.azure.com",
        "https://dev.azure.com",
        "https://api.github.com",
        "https://outlook.office.com/",
        "https://api.somewhereintheinternet.com/",
        "https://graph.microsoft.com",
    }

    for _, api := range apis {
        _, err := http.Get(api)
        if err != nil {
            fmt.Printf("ERROR: %s is down!\n", api)
            continue
        }

        fmt.Printf("SUCCESS: %s is up and running!\n", api)
    }

    elapsed := time.Since(start)
    fmt.Printf("Done! It took %v seconds!\n", elapsed.Seconds())
}
```

Where the output is:
```
SUCCESS: https://management.azure.com is up and running!
SUCCESS: https://dev.azure.com is up and running!
SUCCESS: https://api.github.com is up and running!
SUCCESS: https://outlook.office.com/ is up and running!
ERROR: https://api.somewhereintheinternet.com/ is down!
SUCCESS: https://graph.microsoft.com is up and running!
Done! It took 3.103446568 seconds!
```

Perhaps we can check all sites at the same time? We can use a *goroutine to check each site*, updating the code that makes the HTTP call to the site.

```golang
func checkAPI(api string) {
    _, err := http.Get(api)
    if err != nil {
        fmt.Printf("ERROR: %s is down!\n", api)
        return // we don't need the continue as we are not in a loop. We stop the goroutine execution instead.
    }

    fmt.Printf("SUCCESS: %s is up and running!\n", api)
}
```
So, using the new code we have:

```golang
package main

import (
    "fmt"
    "net/http"
    "time"
)
func checkApi(api string) {
  ...
}

func main() {
    start := time.Now()

    apis := []string{
        "https://management.azure.com",
        "https://dev.azure.com",
        "https://api.github.com",
        "https://outlook.office.com/",
        "https://api.somewhereintheinternet.com/",
        "https://graph.microsoft.com",
    }

    for _, api := range apis {
        go checkAPI(api)
    }
}
```

Where the output is:
```
Done! It took 1.2713e-05 seconds!
```

Even though it doesn't look like the checkAPI function is running, it's running. It just didn't have time to finish. To see the output, we need to include a timer after the loop.

It looks like it's working, right? Well, not precisely. There has to be a better way, and that's what we'll discuss in the next post when we talk about [*channels*]({% post_url 2022-03-12-golang-concurrency-channels %}).


