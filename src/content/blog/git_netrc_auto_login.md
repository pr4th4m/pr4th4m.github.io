---
author: Prathamesh Nevagi
pubDatetime: 2022-12-05T15:22:00Z
title: Git login with Netrc
slug: git-netrc-login
featured: false
draft: false
tags:
  - git
  - netrc
description: Steps to install NFS and SMB server on Raspberry Pi
---

<!--toc:start-->
- [What is Netrc file ?](#what-is-netrc-file)
- [Netrc file:](#netrc-file)
- [Secure netrc file:](#secure-netrc-file)
- [Warning:](#warning)
- [Use case:](#use-case)
<!--toc:end-->

## What is Netrc file ?
Netrc file contains user credentials and is used to auto-login. It is usually located in users home directory `.netrc` but location can be overridden with `NETRC` environment variable. Netrc also supports macros `macdef` to automate tasks. Netrc can be used with ftp, curl, git etc.


## Netrc file:
- Create file named `.netrc` in home directory.
- Lets consider your git server is hosted on domain `git.company.com`

  ```bash
  ~ $ cat .netrc
  machine git.company.com
  login first.last
  password secret-password
  ```

## Secure netrc file:
- As `.netrc` is used to store credentials, lets secure the file.

  ```bash
  ~ $ chmod 0600 ~/.netrc
  ```

That's it, next time when you use git for domain `git.company.com`, git should pick up the credentials on behalf of you ;)


## Warning:
- Netrc stores credentials in plan text. This is how netrc is meant to be ;)


## Use case:
- Its a good idea to use netrc when you have token based authentication.
- Automation for service accounts with token based authentication.
