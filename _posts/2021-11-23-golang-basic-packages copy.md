---
title: Go - Use data types and structs, arrays, slices, and maps in Go (I) (I)
date: 2032-11-26 10:30:00 +01:00
tags: [golang]
description: Go - Use data types and structs, arrays, slices, and maps in Go (I)
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
- [Maps](#maps)

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

# Maps