---
author: Prathamesh Nevagi
pubDatetime: 2022-09-09T15:22:00Z
title: Setup NFS and SMB server on Raspberry Pi
slug: nfs-and-smb-on-raspberry
featured: false
draft: false
tags:
  - nfs
  - samba server
description: Steps to install NFS and SMB server on Raspberry Pi
---

<!--toc:start-->
- [NFS server](#nfs-server)
- [Samba server](#samba-server)
<!--toc:end-->

## NFS server
- Install server

  ```bash
  sudo apt install nfs-common nfs-server
  ```

- Create directory you wanna share

  ```bash
  mkdir /media/storage
  ```

- Edit file `/etc/exports` with below content

  ```bash
  /media/storage 192.168.1.0/24(rw,all_squash,insecure,no_subtree_check)
  # <directory> <who_can_access> <options>
  ```

- Restart services

  ```bash
  sudo service nfs restart
  /etc/init.d/nfs-kernel-server restart
  ```

- Connecting from client (MacOS)

  ```bash
  mkdir /Users/username/nfs_shared
  sudo mount -v -o "resvport" 192.168.1.9:/media/storage /Users/username/nfs_shared
  ```


## Samba server
- Install server

  ```bash
  sudo apt-get install samba samba-common-bin
  ```

- Create samba password for system user

  ```bash
  sudo smbpasswd -a pi
  ```

- Create directory you wanna share

  ```bash
  mkdir /media/storage
  ```

- Edit file `/etc/samba/smb.conf` with below content

  ```bash
  [PI]
  comment = Pi workspace
  path = /media/storage
  create mask = 0775
  directory mask = 0775
  read only = no
  browseable = yes
  public = no
  force user = pi
  only guest = no
  ```

- Restart service

  ```bash
  sudo service samba restart
  ```

- Connecting from client (MacOS)

  ```bash
  mkdir /Users/username/smb_shared
  mount -t smbfs //pi@192.168.1.9/pi /Users/username/smb_shared
  ```
