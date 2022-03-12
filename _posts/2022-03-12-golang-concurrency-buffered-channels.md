---
title: Go - Concurrency - Buffered channels
date: 2022-03-12 17:30:00 +01:00
tags: [golang]
description: Go - Concurrency - Buffered channels
---

Source: [Learn how concurrency works in Go](https://docs.microsoft.com/en-us/learn/modules/go-concurrency/)

- [Buffered channels](#buffered-channels)
  - [Unbuffered vs. buffered channels](#unbuffered-vs-buffered-channels)
- [Channel directions](#channel-directions)
- [Multiplexing](#multiplexing)

# Buffered channels

Channels are unbuffered by default. That means they accept a *send* operation only if there's a *receive* operation. Otherwise, the program will be blocked waiting forever.

*Buffered channels* send and receive data without blocking the program. They behaves like a queue. You can limit the size of this queue, when you crete the channel:

```golang
ch := make(chan int, 10)
```

- Every time you send something to the channel, the element is added to the queue
- A receive operation removes the element from the queue
- When the channel is full, any send operation simply waits until there's space to hold the data
- If the channel is empty and there's a read operation, it's blocked until there's something to read

> We recommend that when you use channels, you always use goroutines

## Unbuffered vs. buffered channels

**Unbuffered channels** communicate *synchronously*. They guarantee that every time you send data, the program gets blocked until someone is reading from the channel.

**Buffered channels** decouple the send and receive operations

# Channel directions

When you use channels as parameters to a function, you can specify whether a channel is meant to *send* or *receive* data:

```golang
chan<- int // it's a channel to only send data
<-chan int // it's a channel to only receive data
```

Example:
```golang
package main

import "fmt"

// Sending data to a channel
func send(ch chan<- string, message string) {
    fmt.Printf("Sending: %#v\n", message)
    ch <- message
}

// Receiving data from a channel
func read(ch <-chan string) {
    fmt.Printf("Receiving: %#v\n", <-ch)
}

func main() {
    ch := make(chan string, 1)
    send(ch, "Hello World!")
    read(ch)
}
```

# Multiplexing

How to interact with more than one channel simultaneously by using the `select` keyword. The `select` statement works like a `switch` statement but for channels.

An essential aspect of the `select` statement is that it finishes its execution *after it processes an event*. If you want to wait for more events to happen, you might need to use a `loop`.

```golang
package main

import (
    "fmt"
    "time"
)

func process(ch chan string) {
    time.Sleep(3 * time.Second)
    ch <- "Done processing!"
}

func replicate(ch chan string) {
    time.Sleep(1 * time.Second)
    ch <- "Done replicating!"
}

func main() {
    ch1 := make(chan string)
    ch2 := make(chan string)
    go process(ch1)
    go replicate(ch2)

    for i := 0; i < 2; i++ {
        select {
        case process := <-ch1:
            fmt.Println(process)
        case replicate := <-ch2:
            fmt.Println(replicate)
        }
    }
}
```

Output:
```
Done replicating!
Done processing!
```



