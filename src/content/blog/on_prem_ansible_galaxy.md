---
author: Prathamesh Nevagi
pubDatetime: 2023-11-03T15:22:00Z
title: On-prem ansible galaxy
slug: ansible-galaxy
featured: false
draft: false
tags:
  - ansible
description: How to install on-prem ansible galaxy
---

<!--toc:start-->
- [On-prem galaxy repository](#on-prem-galaxy-repository)
- [Usage](#usage)
- [Installation](#installation)
<!--toc:end-->

Generic ansible roles can be shared through [ansible galaxy](https://galaxy.ansible.com), however, its hard to access ansible-galaxy from within corporate network. A good alternative is to host something similar to ansible-galaxy on-prem.


## On-prem galaxy repository
- For this blog stash is considered (https://stash.company.com), however, gitlab, gogs or similar should work as well
- Create a new project on stash `Ansible Galaxy` with key `AG`
- Create repo for each generic ansbile role.
- All generic ansbile roles can be browsed at https://stash.company.com/projects/AG
- Now these roles can be used with multiple ansible playbooks.


## Usage
- Create a new file `requirements.yml` alongside ansible `playbook.yml`.
- For this blog purposes I am considering `awscli` as an generic ansible role with its own repo, which resides in our newly created stash project `Ansible Galaxy (AG)`
- Copy/paste below content to `requirements.yml`

  ```yaml
  ---
  name: awscli
  src: git+https://stash.company.com/scm/ag/awscli
  version: master
  path: ~/.ansible/roles

  # name - name of role
  # src - location of role
  # version - which branch the role should be installed from
  # path - where we want to install roles
  ```

- The `requirements.yml` files acts as a role dependency list for our `playbook.yml`


## Installation
- Once we define our ansible playbook dependencies, its time to install them.

  ```bash
  sudo ansible-galaxy install -r requirements.yml
  # NOTE: no need to install ansible-galaxy seperately, it ships with default ansible installation
  ```

- Done! all the roles are now installed and ready to be used by our `playbook.yml`
