---
title: Go - How to log
date: 2022-03-21 17:30:00 +01:00
tags: [golang]
description: Go - How to log
---

Source: [Implement error handling and logging in Go](https://docs.microsoft.com/en-us/learn/modules/go-errors-logs/)

# The log package

```golang
import (
    "log"
)

func main() {
    log.Print("Hey, I'm a log!")
}

// 2020/12/19 13:39:17 Hey, I'm a log!
```

`log.Print()` == `fmt.Print()`, but you can do other things with the `log` package, like sending logs to a file.

## log.Fatal()

You can use the `log.Fatal()` function to log an error and end the program as if you'd used `os.Exit(1)` (stopping the program).

```golang
package main

import (
    "fmt"
    "log"
)

func main() {
    log.Fatal("Hey, I'm an error log!")
    fmt.Print("Can you see me?")
}

// 2020/12/19 13:53:19  Hey, I'm an error log!
// exit status 1
```

## log.Panic()

The `log.Panic()` function, which also calls the `panic()` function (exit with status 2)

## log.SetPrefix()

Add a prefix to your program's log messages:

```golang
func main() {
    log.SetPrefix("main(): ")
    log.Print("Hey, I'm a log!")
    log.Fatal("Hey, I'm an error log!")
}

// main(): 2021/01/05 13:59:58 Hey, I'm a log!
// main(): 2021/01/05 13:59:58 Hey, I'm an error log!
// exit status 1
```

## Logging to a file

```golang
package main

import (
    "log"
    "os"
)

func main() {
    file, err := os.OpenFile("info.log", os.O_CREATE|os.O_APPEND|os.O_WRONLY, 0644)
    if err != nil {
        log.Fatal(err)
    }

    defer file.Close()

    log.SetOutput(file)
    log.Print("Hey, I'm a log!")
}
```

## Logging frameworks

E.g: Logrus, zerolog, zap, and Apex.

E.g: zerolog

```golang
package main

import (
    "github.com/rs/zerolog"
    "github.com/rs/zerolog/log"
)

func main() {
    zerolog.TimeFieldFormat = zerolog.TimeFormatUnix
    log.Print("Hey! I'm a log message!")
}

// {"level":"debug","time":1609855453,"message":"Hey! I'm a log message!"}
```