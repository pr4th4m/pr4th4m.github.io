---
author: Prathamesh Nevagi
pubDatetime: 2024-02-16T15:22:00Z
title: Golang new and make keyword
slug: golang-new-and-make-keyword
featured: true
draft: false
tags:
  - golang
description: Golang `new` and `make` keyword
---

# new and make keyword in go
How does `new` and `make` keywords allocate memory in golang


## `new` keyword:
- `new` is used to allocate memory for a variable of a specific type, including basic types (like int, float64, etc.) and composite types (like structs)
- It returns a pointer to the zero-initialized memory of the specified type
- Use `new` when you need a pointer to a newly allocated zero value of a type
- `p := new(int)` allocates memory on the heap and returns a pointer to it, whereas `p := 0` simply declares a variable on the stack with an initial value of 0

![new](@assets/images/new_keyword.avif)


## `make` keyword:
- The `make` function is used to create a slice with a specific length and capacity. It gives you more control over the initial size and memory allocation of the slice
- `make` is used to create and initialize slices, maps, and channels, which are types that have underlying data structures
- `make` returns the type itself, not a pointer
- Use make when you need to initialize and allocate memory for slices, maps, or channels.
- Memory for the underlying array is immediately allocated based on the specified length and capacity.
- `s := make([]int, 2, 4)` allocates memory on the heap, whereas `s := []int{}` will only allocate memory after appending items to it

![new](@assets/images/make_keyword.avif)
