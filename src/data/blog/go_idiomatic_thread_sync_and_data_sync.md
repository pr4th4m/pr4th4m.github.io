---
author: Prathamesh Nevagi
pubDatetime: 2024-08-12T16:22:00Z
title: Go idiomatic - Thread synchronization
slug: golang-idiomatic-thread-sync
featured: true
draft: false
tags:
  - golang
  - thread synchronization
description: Go idiomatic - Thread synchronization
---

<!--toc:start-->

- [Thread synchronization](#thread-synchronization)
  - [Go idiomatic way](#go-idiomatic-way)
  - [Traditional way](#traditional-way)
  <!--toc:end-->

## Thread synchronization

### Go idiomatic way

- Example of thread synchronization using channels

```go
package main
import (
	"fmt"
)

func main() {
	params := []map[string]any{
		{"cloud": "aws"},
		{"cloud": "gcp"},
		{"cloud": "azure"},
		{"cloud": "oracle"},
	}

	responseChan := make(chan map[string]any, len(params))

	for i, param := range params {
		go func(i int, p map[string]any) {
			p["id"] = i
			httpRequest(p, responseChan)
		}(i, param)
	}

	for i := 0; i < len(params); i++ {
		response := <- responseChan
		fmt.Println(response["id"], response["cloud"])
	}

	close(responseChan)
}

func httpRequest(source map[string]any, responseChan chan map[string]any) {
	responseChan <- source
}
```

### Traditional way

- Example of thread synchronization using WaitGroup

```go
package main
import (
	"fmt"
	"sync"
)

func main() {
	params := []map[string]any{
		{"cloud": "aws"},
		{"cloud": "gcp"},
		{"cloud": "azure"},
		{"cloud": "oracle"},
	}

	responseChan := make(chan map[string]any, len(params))

	var wg sync.WaitGroup
	for i, param := range params {
		wg.Add(1)
		go func(i int, p map[string]any) {
			defer wg.Done()
			p["id"] = i
			httpRequest(p, responseChan)
		}(i, param)
	}

	go func() {
		wg.Wait()
		close(responseChan)
	}()

	for response := range responseChan {
		fmt.Println(response["id"], response["cloud"])
	}

}

func httpRequest(source map[string]any, responseChan chan map[string]any) {
	responseChan <- source
}
```
