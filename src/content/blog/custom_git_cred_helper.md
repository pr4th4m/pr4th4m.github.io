---
author: Prathamesh Nevagi
pubDatetime: 2022-11-19T15:22:00Z
title: Custom git credential helper
slug: git-cred-helper
featured: false
draft: false
tags:
  - git
  - python
description: How to write your own custom git credential helper
---

<!--toc:start-->
- [Git credential helper:](#git-credential-helper)
  - [Simple python cli](#simple-python-cli)
- [Git credentials store](#git-credentials-store)
- [Git helper configuration](#git-helper-configuration)
<!--toc:end-->

## Git credential helper:
Git credential helper is used to save user credentials, so that user does not require to enter credentials on each git operation.
Git provides few default git credential helpers, [see how to use them](https://git-scm.com/docs/gitcredentials).
This blog will demonstrate on how to build custom git credential helper.


### Simple python cli
- Let's write a python cli called `auth_helper.py`

  ```python
  #!/usr/bin/env python
  import argparse

  class Credential(object):
      def get(self):
          # logic to get username/password from auth file
          pass
      def store(self):
          # logic to store username/password to auth file
          # its better to encrypt password if its in plain text
          pass
      def erase(self):
          # logic to delete auth file
          pass

  def main():
      parser = argparse.ArgumentParser()
      parser.add_argument('operation', action="store", type=str,
              help="Git action to be performed (get|store|erase)")
      # parser all arguments
      arguments = parser.parse_args()
      # get credentials
      credentials = Credential()

      if arguments.operation == "get":
          creds = credentials.get()
          print("username={0}".format(creds.get("username")))
          print("password={0}".format(creds.get("password")))
      elif arguments.operation == "store":
          credentials.store()
          # if credentials are already stored do not store again
      elif arguments.operation == "erase":
          credentials.erase()
      else:
          print "Invalid git operation"

  if __name__ == "__main__":
      main()
  ```

- This cli takes three git operations as cli arguments `get`, `store` and `erase`.
- These arguments are not coincident, they are used by git. Let's know more about them.


## Git credentials store
- Git credentials store looks for three arguments
    - `get`: called when triggered git pull, git fetch, git push etc.
    - `store`: called when triggered git pull, git fetch, git push etc.
    - `erase`: if our provided credentials fail, git will fallback to its own credentials prompt, if this fails as well `erase` is called.


## Git helper configuration
- To configure our cli as git helper, trigger the below command

  ```bash
  git config --global credential.https://git.company.com.helper "./path/to/cli/auth_helper.py"
  ```

- This will define `auth_helper.py` under `credential` section of `.gitconfig` file (user's home directory).
- `https://git.company.com` is the domain where git is hosted.


**Done!!** Next time we use git, our `auth_helper.py` should provide credentials for git authorization.
