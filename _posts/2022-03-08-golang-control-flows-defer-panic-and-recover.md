---
title: Go - Control flows - Defer, panic and recover functions
date: 2022-03-08 10:00:00 +01:00
tags: [golang]
description: Go - Control flows - Defer, panic and recover functions
---

Source: [Use control flows in Go](https://docs.microsoft.com/en-us/learn/modules/go-control-flow/)


# Defer function

The `defer` statement postpones the running of a function (including any parameters) until the function that contains the `defer` statement finishes.

When `defer` a function: when you want to avoid forgetting about tasks like **closing a file** or **running a cleanup process**.

The `defer` statements *run in reverse order*, from last to first.

E.g.

```golang
package main

import "fmt"

func main() {
    for i := 1; i <= 4; i++ {
        defer fmt.Println("deferred", -i)
        fmt.Println("regular", i)
    }
}
```

Every time `fmt.Println("deferred", -i)` was deferred, the value for `i` was stored, and its run task was added to a queue.
When the function returns, the queue is processed, and the tasks are executed in reverse order.

Output:
```
regular 1
regular 2
regular 3
regular 4
deferred -4
deferred -3
deferred -2
deferred -1
```

## E.g: close a file after write on it

After you *create or open a file*, you `defer` the `.Close()` function **to avoid forgetting to close the file after you're done.**

```golang
func main() {
    newfile, error := os.Create("learnGo.txt")
    if error != nil {
        fmt.Println("Error: Could not create file.")
        return
    }
    defer newfile.Close()

    if _, error = io.WriteString(newfile, "Learning Go!"); error != nil {
	    fmt.Println("Error: Could not write to file.")
        return
    }

    newfile.Sync()
}
```
# Panic function

The `panic()` function stops the normal flow of control in a Go program. The program then crashes with a log message (you can add any value as an argument but usually you send an error message about why you're panicking).

You can combine it with the `defer` statement, as the `defer` will be executed after the panic.

E.g:

```golang
func highlow(high int, low int) {
    if high < low {
        fmt.Println("Panic!")
        panic("highlow() low greater than high")
    }
    defer fmt.Println("Deferred: highlow(", high, ",", low, ")")
    fmt.Println("Call: highlow(", high, ",", low, ")")

    highlow(high, low + 1)
}

func main() {
    highlow(2, 0)
    fmt.Println("Program finished successfully!")
}
```

When the value of `low` is greater than the value of `high`, the program *panics*. You see the `Panic!` message. At this point, **the control flow is interrupted**, and all the **deferred functions start** to print the `Deferred...` message.

Then the program crashes.

Output:
```
Call: highlow( 2 , 0 )
Call: highlow( 2 , 1 )
Call: highlow( 2 , 2 )
Panic!
Deferred: highlow( 2 , 2 )
Deferred: highlow( 2 , 1 )
Deferred: highlow( 2 , 0 )
panic: highlow() low greater than high

...
```

# Recover function

Like the `panic` but instead of crash, you regain control after a panic.

The previous example but using the `revocer()` instead to recover from the panic:

```golang
func main() {
    defer func() {
	  handler := recover()
        if handler != nil {
            fmt.Println("main(): recover", handler)
        }
    }()

    highlow(2, 0)
    fmt.Println("Program finished successfully!")
}
```

```
Call: highlow( 2 , 0 )
Call: highlow( 2 , 1 )
Call: highlow( 2 , 2 )
Panic!
Deferred: highlow( 2 , 2 )
Deferred: highlow( 2 , 1 )
Deferred: highlow( 2 , 0 )
main(): recover from panic highlow() low greater than high

Program exited.
```

The combination of the `panic` and `recover` functions is the idiomatic way that Go handles exceptions.

