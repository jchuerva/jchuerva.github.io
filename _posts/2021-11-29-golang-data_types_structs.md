---
title: Go - Use data types and structs, arrays, slices, and maps in Go
date: 2021-11-29 17:30:00 +01:00
tags: [golang]
description: Go - Use data types and structs, arrays, slices, and maps in Go
---

Source: [Use data types and structs, arrays, slices, and maps in Go](https://docs.microsoft.com/en-us/learn/modules/go-data-types/)

- [Arrays](#arrays)
  - [Multidimensional arrays](#multidimensional-arrays)
- [Slices](#slices)
  - [Declare and initialize a slice](#declare-and-initialize-a-slice)
  - [Slice items](#slice-items)
  - [Append items](#append-items)
  - [Remove items](#remove-items)
  - [Create copies of slices](#create-copies-of-slices)
  - [Loop in slices](#loop-in-slices)
- [Maps](#maps)
  - [Declare and initialize a map](#declare-and-initialize-a-map)
  - [Add items](#add-items)
  - [Access items](#access-items)
  - [Remove items](#remove-items-1)
    - [Loop in a map](#loop-in-a-map)
- [Structs](#structs)
  - [Declare and initialize a struct](#declare-and-initialize-a-struct)
  - [Struct embedding](#struct-embedding)
  - [Encode and decode structs with JSON](#encode-and-decode-structs-with-json)

# Arrays

To declare an array in Go, you have to define the data type of its elements and the number of elements the array can hold. 

```go
// declare and array of 4 integers (length fixed)
var a [3]int
// populate it
a[1] = 10


// declare and initialize it
cities := [5]string{"New York", "Paris", "Berlin", "Madrid"}
// The array length was determined by the strings you put when you initialized it.
// You're not reserving memory you don't know if you'll end up needing.
fmt.Println("Cities:", cities) //  Cities: [New York Paris Berlin Madrid]

// The array length was determined by the strings you put when you initialized it. You're not reserving memory you don't know if you'll end up needing.
numbers := [...]int{99: -1}
fmt.Println("First Position:", numbers[0]) // First Position: 0
fmt.Println("Last Position:", numbers[99]) // Last Position: -1
fmt.Println("Length:", len(numbers)) // Length: 100
```

## Multidimensional arrays

E.g: a two-dimensional array

```go
var twoD [3][5]int // Here the two dimensional array: 3 rows and 5 columns
for i := 0; i < 3; i++ {
    for j := 0; j < 5; j++ {
        twoD[i][j] = (i + 1) * (j + 1)
    }
    fmt.Println("Row", i, twoD[i])
}
fmt.Println("\nAll at once:", twoD)

// Row 0 [1 2 3 4 5]
// Row 1 [2 4 6 8 10]
// Row 2 [3 6 9 12 15]
// All at once: [[1 2 3 4 5] [2 4 6 8 10] [3 6 9 12 15]]
```

# Slices

A slice is a data structure on top of an array or another slice. With a slice, you can access the whole underlying array or only a subsequence of elements.

A slice has three components:
- Pointer to the first reachable element of the underlying array:  `array[0]`
- Length of the slice. The number of elements in the slice
- Capacity of the slice. The number of elements between the start of the slice and the end of the underlying array.

## Declare and initialize a slice

```go
months := []string{"January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"}
fmt.Println(months) // [January February March April May June July August September October November December]
fmt.Println("Length:", len(months)) // Length: 12
fmt.Println("Capacity:", cap(months)) // Capacity: 12
```

## Slice items

Go allows the slice operator `s[i:p]`:
- `s`  is the slice
- `i` is the index of the first element to be included in the slice
- `p` is the index of the first element to be excluded from the slice


```go
months := []string{"January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"}
quarter1 := months[0:3] // [January February March]
fmt.Println(quarter1, len(quarter1), cap(quarter1)) // [January February March] 3 12

quarter2 := months[3:6] // [April May June]
fmt.Println(quarter2, len(quarter2), cap(quarter2)) // [April May June] 3 9
```

Notice how the length of the slices is the same, but the capacity is different. Let's explore the `quarter2` slice. When you declare this slice, you're saying you want the slice to start at position number three, and the last element is located at position number six. The slice's length is three elements, but the capacity is nine because the underlying array has more elements or positions available but not visible to the slice.

The capacity of a slice only tells you how much you can extend a slice. You could create an extended slice from `quarter2`
```go
months := []string{"January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"}
quarter2 := months[3:6]
fmt.Println(quarter2, len(quarter2), cap(quarter2)) // [April May June] 3 9
quarter2Extended := quarter2[:4]
fmt.Println(quarter2Extended, len(quarter2Extended), cap(quarter2Extended)) // [April May June July] 4 9
```

- `([:4])`: Go assumes that you want the first position of the slice
- `[1:]`: Go assumes that you want the last position of the slice

## Append items

Go offers the `append(slice, element)` built-in function.
The append function then returns a new slice that you store in a variable. It could be the same variable for the slice you're changing.

```go
var numbers []int
numbers = append(numbers, 2) // [2]
numbers = append(numbers, 3, 4, 5) // [2 3 4 5]
```

## Remove items

You can use the slice operator `s[i:p]` to remove items from a slice.

```go
letters := []string{"A", "B", "C", "D", "E"} // [A B C D E]
letters = append(letters[:2], letters[2+1:]...) // [A B D E]
```

## Create copies of slices

Go has a built-in `copy(dst, src []Type)` function to create copies of a slice.
```go
slice2 := make([]string, 3)
copy(slice2, letters[1:4])
```

## Loop in slices

If we want to update the value of each element in a slice, we can use a for loop:
```golang
type MyType struct {
    field string
}

var array [10]MyType

for idx, _ := range array {
    array[idx].field = "foo"
}
```

# Maps

A map in Go is basically a hash table, which is a collection of key and value pairs

## Declare and initialize a map

`map[key_type]value_type`, where `key_type` is the type of the keys and `value_type` is the type of the values.

```go
studentsAge := map[string]int{
  "john": 32,
  "bob":  31,
}
fmt.Println(studentsAge) // map[john:32 bob:31]
```

If you don't want to initialize a map with items, use the `make()` function to create an empty map.
```go
studentsAge := make(map[string]int)
```

## Add items
You only need to define a key and a value.  If the pair doesn't exist, the item is added to the map

```go
studentsAge := make(map[string]int)
studentsAge["john"] = 32
studentsAge["bob"] = 31
fmt.Println(studentsAge) // map[john:32 bob:31]
```

If you create a `nil` map, you get an error adding values to it.

## Access items
Usual subscript notation `m[key]`.

If the key isn't present on a map, the value returned is `zero value` for the map's value type.

```go
studentsAge := make(map[string]int)
studentsAge["bob"] = 31
fmt.Println("Bob's age is", studentsAge["bob"]) // Bob's age is 31
fmt.Println("Christy's age is", studentsAge["christy"]) // Christy's age is 0
```

It's fair that Go doesn't return an error when you access an item that doesn't exist in a map. But there are times where you need to know if an item exists or not

```go
studentsAge := make(map[string]int)
studentsAge["john"] = 32

age, exist := studentsAge["christy"]
if exist {
  fmt.Println("Christy's age is", age) // Christy's age is 0
} else {
  fmt.Println("Christy's age is not defined") // Christy's age is not defined
}
```

## Remove items
Use the built-in `delete()` function. If you try to delete an item that doesn't exist, Go won't panic

```go
studentsAge := make(map[string]int)
studentsAge["john"] = 32
studentsAge["bob"] = 31
delete(studentsAge, "john")
fmt.Println(studentsAge) // map[bob:31]
delete(studentsAge, "christy")
fmt.Println(studentsAge) // map[bob:31]
```

### Loop in a map
Use the `range`-based loop. `Range` yields first the key of an item, and secondly the value of that item. You can ignore either one of them by using the `_` variable.

```go
for name, age := range studentsAge {
  fmt.Printf("%s\t%d\n", name, age)
}
```

# Structs
A struct is a collection of fields in one structure

## Declare and initialize a struct

Use the `struct` keyword, along with the list of fields and their type. To access individual fields of a struct, you can do it by using the dot notation `(.)`

```go
type Employee struct {
  ID        int
  FirstName string
  LastName  string
  Address   string
}

// Declare a variable
var john Employee

// Declare and initialize it
employee := Employee{1001, "John", "Doe", "Doe's Street"}
employee := Employee{LastName: "Doe", FirstName: "John"}

// access to each individual field
employee.ID = 1001
fmt.Println(employee.FirstName) // John
```

Use the `&` operator to yield a pointer to the struct.

```go
employee := Employee{LastName: "Doe", FirstName: "John"}
fmt.Println(employee) // {John Doe }
employeeCopy := &employee
employeeCopy.FirstName = "David"
fmt.Println(employee) // {0 David Doe }
```

## Struct embedding

Embed another struct within a struct to reduce repetition and reuse a common struct.

```go
type Person struct {
  ID        int
  FirstName string
  LastName  string
  Address   string
}
type Employee struct {
  Information Person
  ManagerID   int
}
var employee Employee
employee.Information.FirstName = "John"
```

Instead of this (it's not a good practice), you can simply include a new field with the same name of the struct you're embedding:

```go
type Person struct {
  ID        int
  FirstName string
  LastName  string
  Address   string
}
type Employee struct {
  Person // Embed the Person struct
  ManagerID int
}
var employee Employee
employee.FirstName = "John"
```

## Encode and decode structs with JSON
You can use structs to encode and decode data in JSON

To encode a struct into JSON, you use the `json.Marshal` function. And to decode a JSON string into a data structure, you use the `json.Unmarshal` function.

```go
// To add a good example
```