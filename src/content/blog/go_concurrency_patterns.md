---
author: Prathamesh Nevagi
pubDatetime: 2024-03-06T15:22:00Z
title: Concurrency patterns in go
slug: concurrency-patterns-go
featured: true
draft: false
tags:
  - golang
  - concurrency
description: Concurrency patterns in go
---

# Concurrency patterns in go

<!--toc:start-->
- [Concurrency patterns in go](#concurrency-patterns-in-go)
  - [Pipeline](#pipeline)
  - [Worker pool](#worker-pool)
  - [Fan out Fan in](#fan-out-fan-in)
  - [Rate limiter](#rate-limiter)
  - [All patterns](#all-patterns)
<!--toc:end-->


## Pipeline
Output of one channel is input to other channel
![pipeline](@assets/images/pipeline_concurrency_pattern.png)
<details>
  <summary>Code snippet</summary>

  ```go
  package main

  import (
      "fmt"
      "time"
  )

  type Order struct {
      name string
  }

  type RestaurantPipe interface {
      accept(orders []string) chan string
      prepare(acceptedOrders <-chan string) chan string
      ready(preparedOrders <-chan string) chan string
  }

  type OrderPipeline struct {
      orders []Order
  }

  func (o *OrderPipeline) accept() chan string {
      acceptedOrders := make(chan string)
      go func() {
          for _, order := range o.orders {
              fmt.Println("Order accepted:", order.name)
              acceptedOrders <- order.name
          }
          close(acceptedOrders)
      }()
      return acceptedOrders
  }

  func (o *OrderPipeline) prepare(acceptedOrders <-chan string) chan string {
      preparedOrders := make(chan string)
      // NOTE:
      // We have to use goroutine to iterator over channel here
      // as unbuffered channel will block operation
      // If channel was buffered there's no need of goroutine in that case
      // example: preparedOrders := make(chan string, 3)
      go func() {
          for o := range acceptedOrders {
              time.Sleep(time.Second)
              fmt.Println("Order prepared:", o)
              preparedOrders <- o
          }
          close(preparedOrders)
      }()
      return preparedOrders
  }

  func (o *OrderPipeline) ready(preparedOrders <-chan string) chan string {
      readyToServe := make(chan string)
      go func() {
          for o := range preparedOrders {
              fmt.Println("Order ready:", o)
              readyToServe <- o
          }
          close(readyToServe)
      }()
      return readyToServe
  }

  func main() {

      orders := []Order{
          {"pizza"},
          {"pasta"},
          {"salad"},
          {"soup"},
          {"bread"},
      }

      pipeline := &OrderPipeline{orders}
      acceptedOrders := pipeline.accept()
      preparedOrders := pipeline.prepare(acceptedOrders)
      readyToServe := pipeline.ready(preparedOrders)

      for o := range readyToServe {
          fmt.Println("Server order:", o)
      }
  }
  ```

</details>


## Worker pool
Worker pool is a load balancing mechanism
![pipeline](@assets/images/worker_pool_concurrency_pattern.png)
<details>
  <summary>Code snippet</summary>

  ```go
  package main

  import (
      "fmt"
      "time"
  )

  type Order struct {
      name string
  }

  type ChefWorkerPool struct {
      orders  []Order
      workers int
  }

  func (c *ChefWorkerPool) acceptOrder() chan string {
      acceptedOrders := make(chan string, c.workers)
      go func() {
          for _, order := range c.orders {
              fmt.Println("Order accepted", order.name)
              acceptedOrders <- order.name
              time.Sleep(time.Second)
          }
          close(acceptedOrders)
      }()
      return acceptedOrders
  }

  func (c *ChefWorkerPool) prepareOrder(chefID int, acceptedOrders <-chan string, preparedOrders chan<- string) {
      for a := range acceptedOrders {
          time.Sleep(2 * time.Second)
          fmt.Printf("Order %s prepared by chef %d\n", a, chefID)
          preparedOrders <- a
      }
  }

  func (c *ChefWorkerPool) startCooking(acceptedOrders <-chan string) {
      preparedOrders := make(chan string)
      for w := 1; w <= c.workers; w++ {
          go c.prepareOrder(w, acceptedOrders, preparedOrders)
      }

      defer close(preparedOrders)
      for o := 1; o <= len(c.orders); o++ {
          fmt.Println("Order ready", <-preparedOrders)
      }
  }

  func main() {

      orders := []Order{
          {"pizza"},
          {"pasta"},
          {"salad"},
          {"soup"},
          {"bread"},
      }

      chefPool := ChefWorkerPool{orders, 2}
      chefPool.startCooking(chefPool.acceptOrder())
  }
  ```

</details>

## Fan out Fan in 
Fan out and fan in are used to distribute work across multiple goroutines
![pipeline](@assets/images/fan_out_fan_in_concurrency_pattern.png)
<details>
  <summary>Code snippet</summary>

  ```go
  package main

  import (
      "fmt"
      "time"
  )

  type OrderInterface interface {
      getName() string
  }

  type FoodOrder struct {
      name string
  }

  func (o *FoodOrder) getName() string {
      return o.name
  }

  type DrinkOrder struct {
      name string
  }

  func (o *DrinkOrder) getName() string {
      return o.name
  }

  func prepareOrder[T OrderInterface](duration time.Duration, orders []T) chan string {
      completedOrders := make(chan string, 3)
      go func() {
          for _, o := range orders {
              fmt.Println("Order accepted:", o.getName())
              time.Sleep(duration)
              fmt.Println("Order completed:", o.getName())
              completedOrders <- o.getName()
          }
          close(completedOrders)
      }()
      return completedOrders
  }

  func completeOrder(completedOrders ...<-chan string) chan string {
      packOrders := make(chan string, 3)

      go func() {
          for _, co := range completedOrders {
              for o := range co {
                  packOrders <- o
              }
          }
          close(packOrders)
      }()

      return packOrders
  }

  func main() {

      // Consider we have a big order for a party
      // Order would contain food and drinks
      // We will consider food and drinks as separate order
      // i.e fan out food and drinks and when done fan in i.e merge both orders
      foodOrder := []*FoodOrder{
          &FoodOrder{"pizza"},
          &FoodOrder{"pasta"},
          &FoodOrder{"salad"},
          &FoodOrder{"soup"},
          &FoodOrder{"bread"},
      }

      drinkOrder := []*DrinkOrder{
          &DrinkOrder{"water"},
          &DrinkOrder{"juice"},
          &DrinkOrder{"milk"},
          &DrinkOrder{"tea"},
          &DrinkOrder{"coffee"},
      }

      orders := completeOrder(
          prepareOrder(time.Second*2, foodOrder),
          prepareOrder(time.Second*1, drinkOrder),
      )
      for o := range orders {
          fmt.Println("Order packed:", o)
      }
  }
  ```

</details>

## Rate limiter
Rate limiter is a throttling mechanism
![pipeline](@assets/images/rate_limiter_concurrency_pattern.png)
<details>
  <summary>Code snippet</summary>

  ```go
  package main

  import (
      "fmt"
      "time"
  )

  type Order struct {
      name string
  }

  type OrderRateLimiter struct {
      limiter *time.Ticker
      orders  []Order
  }

  func (o *OrderRateLimiter) accept() chan string {
      acceptedOrders := make(chan string)
      go func() {
          for _, or := range o.orders {
              fmt.Println("Order accepted", or.name)
              acceptedOrders <- or.name
          }
          close(acceptedOrders)
      }()
      return acceptedOrders
  }

  func (o *OrderRateLimiter) prepare(acceptedOrders <-chan string) chan string {
      preparedOrders := make(chan string)
      go func() {
          for ao := range acceptedOrders {
              <-o.limiter.C
              fmt.Println("Order prepared", ao, time.Now())
              preparedOrders <- ao
          }
          close(preparedOrders)
      }()
      return preparedOrders
  }

  func (o *OrderRateLimiter) ready(preparedOrders <-chan string) {
      for po := range preparedOrders {
          fmt.Println("Order ready", po)
      }
  }

  func main() {
      orders := []Order{
          {"pizza"},
          {"pasta"},
          {"salad"},
          {"soup"},
          {"bread"},
      }

      orderRateLimiter := &OrderRateLimiter{
          limiter: time.NewTicker(2 * time.Second),
          orders:  orders,
      }

      acceptedOrders := orderRateLimiter.accept()
      preparedOrders := orderRateLimiter.prepare(acceptedOrders)
      orderRateLimiter.ready(preparedOrders)
  }
  ```

</details>

## All patterns
![pipeline](@assets/images/concurrency_patterns.avif)
