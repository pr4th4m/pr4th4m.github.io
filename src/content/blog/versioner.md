---
author: Prathamesh Nevagi
pubDatetime: 2024-01-29T15:22:00Z
title: SemVer versioning utility
slug: semver-versioning
featured: false
draft: false
tags:
  - python
  - semver
description: Netrc python client
---

<!--toc:start-->
- [Versioner](#versioner)
  - [Installation](#installation)
  - [Current support](#current-support)
  - [Usage](#usage)
- [Development](#development)
- [NOTE](#note)
<!--toc:end-->

## Versioner
Version reader/writer for popular package managers as per [SemVer](https://semver.org/)

### Installation

  ```bash
  pip install git+https://github.com/VeritasOS/versioner.git
  ```

### Current support
- npm
- dep
- json
- toml
- yaml (currently multi doc in single file not supported)


### Usage
- Read version (run command from project root)

  ```bash
  > versioner read npm  # projects based on npm
  "0.0.1"

  > versioner read dep  # golang projects
  "0.1.1"
  ```

  - Write version (run command from project root)

  ```bash
  # package.json
  ....
  "version": "1.1.1",
  ....

  # Release patch version
  > versioner write npm --inc-patch

  # package.json
  ....
  "version": "1.1.2",
  ....

  # Release minor version
  > versioner write npm --inc-minor

  ....
  "version": "1.2.0",
  ....

  # Release major version
  > versioner write npm --inc-major

  ....
  "version": "2.0.0",
  ....
  ```

  - Read version from custom file

  ```bash
  > versioner read yaml --file /root/app/version.yaml --key-depth metadata,version
  0.0.2

  version.yaml
  ---
  metadata:
    version: 0.0.2
  ```

- Write version to custom file

  ```bash
  > versioner write yaml --inc-major --file /root/app/version.yaml --key-depth metadata,version

  version.yaml
  ---
  metadata:
    version: 1.0.0
  ```

## Development

  ```bash
  git clone https://github.com/VeritasOS/versioner.git
  cd versioner
  pip install -e .
  ```

## NOTE
- This utility is tested for npm and dep.
- Read/write for custom file may break, please feel free to send a patch.
