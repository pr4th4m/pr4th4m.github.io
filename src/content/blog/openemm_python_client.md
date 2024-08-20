---
author: Prathamesh Nevagi
pubDatetime: 2023-04-18T15:22:00Z
title: OpenEMM python client
slug: openemm-python-client
featured: false
draft: false
tags:
  - python
  - openemm
description: OpenEMM python client
---

<!--toc:start-->
- [Py-OpenEMM](#py-openemm)
  - [Pre-requisites](#pre-requisites)
  - [Installation](#installation)
  - [Configuration](#configuration)
  - [Usage](#usage)
<!--toc:end-->

## Py-OpenEMM
[**OpenEMM**](http://www.openemm.org) is a feature-rich web-based enterprise application for email marketing, newsletters and service mails.

### Pre-requisites
- Python 2.7 or higher
- Python Suds 0.4 or higher


### Installation
- Clone repo

  ```bash
  git clone https://github.com/pr4th4m/py-openemm.git
  ```

### Configuration
- Move to directory where you have cloned the repo

  ```bash
  cd py-openemm/pyopenemm
  vi config.py
  ```

- Open `config.py` file and enter openemm wsdl url, username and password

  ```bash
  OPENEMM_URL = 'http://127.0.0.1:8080/cms_services/urn:agnitas-webservice?wsdl'
  WEBSERVICE_USER = 'test_user'
  WEBSERVICE_PASSWORD = 'test_123'
  ```

### Usage
- Create client connection

  ```python
  from pyopenemm.connection.connect import create_connection
  client = create_connection()
  # connect to webservice , returned is suds client
  ```

- Get OpenEMM client

  ```python
  from pyopenemm.webservice.service import OpenEMM
  openemm = OpenEMM(client)
  ```

- Find subscriber

  ```python
  # get subcriber by email
  subscriber = openemm.find_subscriber(('email','test@gmail.com'))
  ```

- Get subscriber details

  ```python
  subscriber_details = openemm.get_subscriber(subscriber)
  ```

- Add new subcriber

  ```python
  user_dict = {'email':'openemm@openemm.com','firstname':'openemmfirstname','lastname':'openemmlastname','gender':0}
  new_subscriber = openemm.add_subscriber(user_dict,True,'email',True) # add new subscriber to openemm

  # Four parameters of add_subscriber() method
  # user_dict = Dictionary containing user information
  # double_check - If True, check if subscriber is already in database
  # key_column - column used for double_check
  # overwrite - If True, subscriber gets updated
  ```

[**Feel free to Contribute back**](https://github.com/pr4th4m/py-openemm)
