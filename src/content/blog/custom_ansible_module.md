---
author: Prathamesh Nevagi
pubDatetime: 2023-10-07T15:22:00Z
title: Custom ansible modules
slug: custom-ansible-modules
featured: true
draft: false
tags:
  - python
  - ansible
description: Custom ansible modules
---

<!--toc:start-->
- [How to write ansible custom modules](#how-to-write-ansible-custom-modules)
- [Module](#module)
- [Usage](#usage)
<!--toc:end-->

## How to write ansible custom modules
Ansible modules are easy way to interact between an existing application and ansible playbook. This blog covers how to write your own ansible module.


## Module
- Create a python file called `greet.py` with following content

  ```python
  def main():
      module = AnsibleModule(
          argument_spec = dict(
              message = dict(required=True, type="str"),
          ),
          supports_check_mode=True
      )

      # get module params
      message = module.params.get("message")

      try:
          module.exit_json(changed=True, msg=message)
      except:
          module.fail_json(msg="It's bad not to greet someone")

  from ansible.module_utils.basic import AnsibleModule
  if __name__ == "__main__":
      main()
  ```

- File name `greet.py` is considered as ansible module.
- Arguments defined in ansible playbooks will be parsed by `AnsibleModule` class.
- Ansible module always returns json, for convenience ansible provides two methods, one for success and other for failure

  ```python
  # For success
  module.exit_json(changed=True, msg=message)

  # For failure
  module.fail_json(msg="It's bad not to greet someone")
  ```

## Usage
- Create an ansible playbook called `playbook.yml`

  ```bash
  touch playbook.yml
  ```

- Create directory `library` alongside `playbook.yml` and copy `greet.py` to `library`

  ```bash
  mkdir library
  cp greet.py library
  ```

- Now our directory structure should look like

  ```bash
  library
      |- greet.py
  playbook.yml
  ```

- We can now use `greet` module with our `playbook.yml`

  ```yaml
  ---
  - hosts: localhost
    tasks:
    - name: Lets start greeting
      greet:
          message: "Good morning"
  ```
