---
author: Prathamesh Nevagi
pubDatetime: 2024-02-29T15:22:00Z
title: Parallelism vs Concurrency
slug: parallelism-vs-concurrency
featured: true
draft: false
tags:
  - concurrency
  - parallelism
description: Parallelism vs Concurrency
---

# Parallelism Vs Concurrency
- Concurrency is dealing with multiple things at once (does not need to be done at the same time) with some time schedule while parallelism is doing the things at once at same time
- Different parts of the program are executed independently in concurrency
- Different parts of the program are executed at the same time in parallelism
- Concurrency models are OS threads, Green threads, Async IO
- Parallelism models are OS threads, Green threads
- Generally concurrency is good for IO bound operations while parallelism is good for CPU bound operations
- IO bound are network connections, disk operations, api calls etc
- CPU bound are image processing, machine learning etc

![concurrency_vs_parallelism](@assets/images/concurrency_vs_parallelism.avif)
