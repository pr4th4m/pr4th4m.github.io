---
author: Prathamesh Nevagi
pubDatetime: 2024-04-19T15:22:00Z
title: Golang unbuffered vs buffered channels
slug: golang-unbuffered-vs-buffered-channels
featured: true
draft: false
tags:
  - golang
description: Golang unbuffered vs buffered channels
---

# Unbuffered vs Buffered channels

![new](@assets/images/buffered_vs_unbuffered_channels.avif)

## Unbuffered channels
- By default channels are unbuffered, for a sender there should be a corresponding receiver
- There's no need for any other synchronization mechanism like mutexes as for each sender there's only one receiver
- By default unbuffered channels are blocking
- You can send values into channels from one goroutine and receive those values into another goroutine
- Do not communicate by sharing memory instead share memory by communicating

```go
package main
import (
	"fmt"
	"time"
)

func main() {
	// unbuffered channels have 0 cap by default
	oneChef := 0
	order := make(chan int, oneChef)
	customers := []int{1, 2, 3, 4, 5}

	go func() {
		for c := range customers {
			order <- c
			fmt.Printf("Placed order for customer %d\n", c)
		}
		close(order)
	}()

	for o := range order {
		fmt.Printf("Preparing order for customer %d\n", o)
		time.Sleep(1 * time.Second)
		fmt.Printf("Ready order for customer %d\n", o)
	}
}
```


## Buffered channels
- Buffered channels accept a limited number of values without a corresponding receiver for those values
- Buffered channels allow a specified number of values to be sent without blocking until the buffer is full
- Its like creating a buffer inside channel where all data will be kept temporarily until its received by receiver
- Buffered channels have to be synchronized manually i,e main process will not fail even if receiver doesn't exist

```go
package main
import (
	"fmt"
	"time"
)

func main() {
	// buffered channels with 2 cap
	threeChefs := 2
	order := make(chan int, threeChefs)
	customers := []int{1, 2, 3, 4, 5}

	go func() {
		for c := range customers {
			order <- c
			fmt.Printf("Placed order for customer %d\n", c)
		}
		close(order)
	}()

	for o := range order {
		fmt.Printf("Preparing order for customer %d\n", o)
		time.Sleep(1 * time.Second)
		fmt.Printf("Ready order for customer %d\n", o)
	}
}
```
