---
title: Go - Concurrency - Channels
date: 2022-03-12 17:00:00 +01:00
tags: [golang]
description: Go - Concurrency - Channels
---

Source: [Learn how concurrency works in Go](https://docs.microsoft.com/en-us/learn/modules/go-concurrency/)

- [Syntax](#syntax)
  - [Operations](#operations)
    - [Send and receive data](#send-and-receive-data)
    - [Close a channel](#close-a-channel)
- [Example done in the goroutine post](#example-done-in-the-goroutine-post)
  - [Unbuffered channels](#unbuffered-channels)

A channel in Go is a communication mechanism between [*goroutines*]({% post_url 2022-03-11-golang-concurrency-goroutines %}). The idea is to send a value from one goroutine to another, using channels.

# Syntax

A channel sends and receives data, to we need to indicate the type of data we want to send and receive. keyword `chan` as the data type for a channel, but you also need to specify the data type that will pass through the channel, like an `int` type. 

To create a channel, you use the built-in make() function, like:

```golang
ch := make(chan int)
```

## Operations

### Send and receive data

A channel can do two operations: send data and receive data. *Sending data and receiving data in channels are blocking operations*.

```golang
ch <- x // sends (or write) x through channel ch
x = <-ch // x receives (or reads) data sent to the channel ch
<-ch // receives data, but the result is discarded
```

### Close a channel

When closing a channel, we are saying that data will not be sent in that channel again.

```golang
close(ch)
```
If you try to *send* data to a closed channel, the program will panic.If you try to *read* data, it will return a zero value.

# Example done in the goroutine post

```golang
func checkAPI(api string, ch chan string) {
    _, err := http.Get(api)
    if err != nil {
        ch <- fmt.Sprintf("ERROR: %s is down!\n", api)
        return
    }

    ch <- fmt.Sprintf("SUCCESS: %s is up and running!\n", api)
}
```

So the code is:

```golang
package main

import (
	"fmt"
	"net/http"
	"time"
)

func checkAPI(api string, ch chan string) {
	_, err := http.Get(api)
	if err != nil {
    // Create a string
		ch <- fmt.Sprintf("ERROR: %s is down!\n", api)
		return // we don't need the continue as we are not in a loop. We stop the goroutine execution instead.
	}
	ch <- fmt.Sprintf("SUCCESS: %s is up and running!\n", api)
}

func main() {
	start := time.Now()
	ch := make(chan string)

	apis := []string{
		"https://management.azure.com",
		"https://dev.azure.com",
		"https://api.github.com",
		"https://outlook.office.com/",
		"https://api.somewhereintheinternet.com/",
		"https://graph.microsoft.com",
	}

	for _, api := range apis {
		go checkAPI(api, ch)
	}

	elapsed := time.Since(start)
	fmt.Print(<-ch)
	fmt.Printf("Done! It took %v seconds!\n", elapsed.Seconds())
}
```

The output is:

```
ERROR: https://api.somewhereintheinternet.com/ is down!

Done! It took 0.007401217 seconds!
```

We only see the output for the first goroutine.

## Unbuffered channels

Unbuffered channels block the sending operation until there's someone ready to receive the data.

`fmt.Print(<-ch)` blocks the program until there's someone ready to receive the data.

We can add more `fmt.Print(<-ch)`  lines to keep reading the data from the channel... but this is not the best solution... we could add a `for` loop to keep reading the data from the channel.

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

    ch := make(chan string)

    for _, api := range apis {
        go checkAPI(api, ch)
    }

    // Read all values from the channel
    for i := 0; i < len(apis); i++ {
        fmt.Print(<-ch)
    }

    elapsed := time.Since(start)
    fmt.Printf("Done! It took %v seconds!\n", elapsed.Seconds())
}

func checkAPI(api string, ch chan string) {
    _, err := http.Get(api)
    if err != nil {
        ch <- fmt.Sprintf("ERROR: %s is down!\n", api)
        return
    }

    ch <- fmt.Sprintf("SUCCESS: %s is up and running!\n", api)
}
```



