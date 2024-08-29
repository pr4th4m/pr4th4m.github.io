---
author: Prathamesh Nevagi
pubDatetime: 2024-02-07T15:22:00Z
title: Stream cli output over http
slug: stream-cli-output
featured: true
draft: false
tags:
  - golang
description: Stream cli output over http
---

<!--toc:start-->
- [Runnel](#runnel)
  - [Installation:](#installation)
  - [Usage:](#usage)
  - [Package:](#package)
  - [Detailed documentation:](#detailed-documentation)
<!--toc:end-->

## Runnel
A program to stream any command line output over http. This is useful when we want to display command output on client side. One such use case can be displaying live logs in browser which are running inside a container on server side.

### Installation:
- Using docker (recommended)

  ```bash
  git clone https://github.com/VeritasOS/runnel.git
  cd runnel

  # build docker image
  docker build -t runnel:latest .

  # start runnel server
  docker run --name runnel -d -p 127.0.0.1:9090:9090 runnel:latest
  ```

- Using binary (not recommended, because system dependencies are taken care in docker file on behalf of you)

  ```bash
  git clone https://github.com/VeritasOS/runnel.git
  cd runnel

  ./bin/linux_64/runnel_server -p localhost:9090
  ```

### Usage:
- Trigger below commands to get live stream

  ```bash
  # Fire command with curl
  curl -X POST -H 'Content-Type: application/json' -d '{"cmd":"ping -c 3 google.com"}' http://localhost:9090/command

  # Get live output stream
  # replace uuid with one which you get from above command
  while true; do curl -sS -H 'Content-Type: application/json' 'http://localhost:9090/stream/fd4b1a38-94f4-4eba-80e7-50578ac4baae' | jq '.response'; done
  ```

### Package:
- Dependency: Install redis server
- Use as golang package

  ```bash
  # Get lib
  go get github.com/veritasos/runnel/runnel

  # Fire command
  client := runnel.NewClient()
  key, err := client.RunCommand("ping", "-c 2 google.com")

  # Get output stream
  client := runnel.NewClient()
  output, err := client.Stream(key, 10)
  ```

### Detailed documentation:
- A much more detailed documentation is provided here [**runnel**](https://github.com/VeritasOS/runnel)
- [**Feel free to Contribute back**](https://github.com/VeritasOS/runnel)
