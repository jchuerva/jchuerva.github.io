---
title: Go - Control flows - Switch
date: 2022-01-12 09:00:00 +01:00
tags: [golang]
description: Go - Control flows - Switch
---

Source: [Use control flows in Go](https://docs.microsoft.com/en-us/learn/modules/go-control-flow/)

- [Syntax](#syntax)
  - [Use multiple expressions](#use-multiple-expressions)
  - [Invoke a function](#invoke-a-function)
  - [Omit a condition](#omit-a-condition)
  - [Make the logic fall through to the next case](#make-the-logic-fall-through-to-the-next-case)

# Syntax

You use switch statements to avoid chaining multiple `if` statements.

```golang
func main() {
    sec := time.Now().Unix()
    rand.Seed(sec)
    i := rand.Int31n(10)

    switch i {
    case 0:
        fmt.Print("zero...")
    case 1:
        fmt.Print("one...")
    case 2:
        fmt.Print("two...")
    }

    fmt.Println("ok")
}
```

Go executes each case of the `switch` statement until it finds a match for the condition.

We can include a `default` case to execute if no other case matches.

```golang
switch i {
case 0:
    fmt.Print("zero...")
case 1:
    fmt.Print("one...")
case 2:
    fmt.Print("two...")
default:
    fmt.Print("no match...")
}
```

## Use multiple expressions

We can use multiple expressions in a `switch` statement, separate by using commas (`,`).

```golang
func location(city string) (string, string) {
    var region string
    var continent string
    switch city {
    case "Delhi", "Hyderabad", "Mumbai", "Chennai", "Kochi":
        region, continent = "India", "Asia"
    case "Lafayette", "Louisville", "Boulder":
        region, continent = "Colorado", "USA"
    case "Irvine", "Los Angeles", "San Diego":
        region, continent = "California", "USA"
    default:
        region, continent = "Unknown", "Unknown"
    }
    return region, continent
}
```

## Invoke a function

A `swtich` statement can also invoke a function:

```golang
func main() {
    switch time.Now().Weekday().String() {
    case "Monday", "Tuesday", "Wednesday", "Thursday", "Friday":
        fmt.Println("It's time to learn some Go.")
    default:
        fmt.Println("It's weekend, time to rest!")
    }

    fmt.Println(time.Now().Weekday().String())
}
```

Or we can call a function from a `case` statement:

```golang
func main() {
    var email = regexp.MustCompile(`^[^@]+@[^@.]+\.[^@.]+`)
    var phone = regexp.MustCompile(`^[(]?[0-9][0-9][0-9][). \-]*[0-9][0-9][0-9][.\-]?[0-9][0-9][0-9][0-9]`)

    contact := "foo@bar.com"

    switch {
      // Note the switch block as no validating expression. See the next section.
    case email.MatchString(contact):
        fmt.Println(contact, "is an email")
    case phone.MatchString(contact):
        fmt.Println(contact, "is a phone number")
    default:
        fmt.Println(contact, "is not recognized")
    }
}
```

## Omit a condition

We can omit a condition in a `switch` statement. We are comparing it to `true` so we are forcing to run the `switch` block all the time.

```golang
func main() {
    rand.Seed(time.Now().Unix())
    r := rand.Float64()
    switch {
    case r > 0.1:
        fmt.Println("Common case, 90% of the time")
    default:
        fmt.Println("10% of the time")
    }
}
```

## Make the logic fall through to the next case

In `Go` there is no `break` implementation inside a switch, as it's the default behavior. If we want to fall through to the next case, we can use the `fallthrough` keyword.

```golang
func main() {
    switch num := 15; {
    case num < 50:
        fmt.Printf("%d is less than 50\n", num)
        fallthrough
    case num > 100:
        fmt.Printf("%d is greater than 100\n", num)
        fallthrough
    case num < 200:
        fmt.Printf("%d is less than 200", num)
    }
}
```

The output is:
```
15 is less than 50
15 is greater than 100
15 is less than 200
```
