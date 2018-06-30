---
layout: post
categories: Linux Postgres
title: Downgrade PostgreSQL Version
date:   2018-06-30 18:00:00 -0400
comments: true
image: /assets/img/elephant.jpg
description: How to downgrade PostgreSQL Version from 9.6.6 to 9.6.3
tags: PostgreSQL postgresql postgres linux yum  
published: true
---
![elephant.jpg][elephant.jpg]

[elephant.jpg]: /assets/img/elephant.jpg

First, check your `yum history`
```bash
[root@pgdb1~]# yum history
```
```
Loaded plugins: fastestmirror
ID     | Login user               | Date and time    | Action(s)      | Altered
-------------------------------------------------------------------------------
    52 | root <root>              | 2017-11-10 10:11 | Update         |    5
    51 | root <root>              | 2017-11-10 09:49 | Update         |    1

```
```bash
[root@pgdb1~]# yum history list 52
```
```
Loaded plugins: fastestmirror
ID     | Command line             | Date and time    | Action(s)      | Altered
-------------------------------------------------------------------------------
    52 | update postgresql96-9.6. | 2017-11-10 10:11 | Update         |    5
```

I tried the undo operation but it failed.
```bash
[root@pgdb1~]# yum history undo 52
```
``` 
Loaded plugins: fastestmirror
Undoing transaction 52, from Fri Nov 10 10:11:23 2017
    Updated postgresql96-9.6.3-1PGDG.rhel7.x86_64         @pgdg96
    Update               9.6.6-1PGDG.rhel7.x86_64         @pgdg96
    Updated postgresql96-contrib-9.6.3-1PGDG.rhel7.x86_64 @pgdg96
    Update                       9.6.6-1PGDG.rhel7.x86_64 @pgdg96
    Updated postgresql96-devel-9.6.3-1PGDG.rhel7.x86_64   @pgdg96
    Update                     9.6.6-1PGDG.rhel7.x86_64   @pgdg96
    Updated postgresql96-libs-9.6.3-1PGDG.rhel7.x86_64    @pgdg96
    Update                    9.6.6-1PGDG.rhel7.x86_64    @pgdg96
    Updated postgresql96-server-9.6.3-1PGDG.rhel7.x86_64  @pgdg96
    Update                      9.6.6-1PGDG.rhel7.x86_64  @pgdg96
Trying other mirror.
Loading mirror speeds from cached hostfile
 * base: centos.mirror.iweb.ca
 * epel: mirror.upb.edu.co
 * extras: centos.mirror.iweb.ca
 * updates: centos.mirror.iweb.ca
Failed to downgrade: postgresql96-9.6.3-1PGDG.rhel7.x86_64
Failed to downgrade: postgresql96-contrib-9.6.3-1PGDG.rhel7.x86_64
Failed to downgrade: postgresql96-devel-9.6.3-1PGDG.rhel7.x86_64
Failed to downgrade: postgresql96-libs-9.6.3-1PGDG.rhel7.x86_64
Failed to downgrade: postgresql96-server-9.6.3-1PGDG.rhel7.x86_64
```

Getting more information with `YUM`
```bash
[root@pgdb1~]# yum history info 52
```
```
Loaded plugins: fastestmirror
Transaction ID : 52
Begin time     : Fri Nov 10 10:11:23 2017
Begin rpmdb    : 711:b0628cdb6f7da51c1d8b71693a77ec3d7709b53a
End time       :            10:11:27 2017 (4 seconds)
End rpmdb      : 711:b63c2ca3ecf481591da01a0142790c266eb14032
User           : root <root>
Return-Code    : Success
Command Line   : update postgresql96-9.6.5
Transaction performed with:
    Installed     rpm-4.11.1-25.el7.x86_64                      @base
    Installed     yum-3.4.3-125.el7.centos.noarch               @base
    Installed     yum-plugin-fastestmirror-1.1.31-29.el7.noarch @base
Packages Altered:
    Updated postgresql96-9.6.3-1PGDG.rhel7.x86_64         @pgdg96
    Update               9.6.6-1PGDG.rhel7.x86_64         @pgdg96
    Updated postgresql96-contrib-9.6.3-1PGDG.rhel7.x86_64 @pgdg96
    Update                       9.6.6-1PGDG.rhel7.x86_64 @pgdg96
    Updated postgresql96-devel-9.6.3-1PGDG.rhel7.x86_64   @pgdg96
    Update                     9.6.6-1PGDG.rhel7.x86_64   @pgdg96
    Updated postgresql96-libs-9.6.3-1PGDG.rhel7.x86_64    @pgdg96
    Update                    9.6.6-1PGDG.rhel7.x86_64    @pgdg96
    Updated postgresql96-server-9.6.3-1PGDG.rhel7.x86_64  @pgdg96
    Update                      9.6.6-1PGDG.rhel7.x86_64  @pgdg96
```

Now we need to downgrade the packages that were updated, indicating the version that we want

```bash
[root@pgdb1~]# yum downgrade postgresql96-9.6.5 postgresql96-contrib-9.6.5 postgresql96-devel-9.6.5 postgresql96-libs-9.6.5 postgresql96-server-9.6.5 postgresql96-debuginfo-9.6.5
```
```
Loaded plugins: fastestmirror
Trying other mirror.
Loading mirror speeds from cached hostfile
 * base: centos.mirror.iweb.ca
 * epel: mirror.upb.edu.co
 * extras: centos.mirror.iweb.ca
 * updates: centos.mirror.iweb.ca
Resolving Dependencies
--> Running transaction check
---> Package postgresql96.x86_64 0:9.6.5-1PGDG.rhel7 will be a downgrade
---> Package postgresql96.x86_64 0:9.6.6-1PGDG.rhel7 will be erased
---> Package postgresql96-contrib.x86_64 0:9.6.5-1PGDG.rhel7 will be a downgrade
---> Package postgresql96-contrib.x86_64 0:9.6.6-1PGDG.rhel7 will be erased
---> Package postgresql96-devel.x86_64 0:9.6.5-1PGDG.rhel7 will be a downgrade
---> Package postgresql96-devel.x86_64 0:9.6.6-1PGDG.rhel7 will be erased
---> Package postgresql96-libs.x86_64 0:9.6.5-1PGDG.rhel7 will be a downgrade
---> Package postgresql96-libs.x86_64 0:9.6.6-1PGDG.rhel7 will be erased
---> Package postgresql96-server.x86_64 0:9.6.5-1PGDG.rhel7 will be a downgrade
---> Package postgresql96-server.x86_64 0:9.6.6-1PGDG.rhel7 will be erased
--> Finished Dependency Resolution

Dependencies Resolved

=======================================================================================================================================================
 Package                                    Arch                         Version                                    Repository                    Size
=======================================================================================================================================================
Downgrading:
 postgresql96                               x86_64                       9.6.5-1PGDG.rhel7                          pgdg96                       1.4 M
 postgresql96-contrib                       x86_64                       9.6.5-1PGDG.rhel7                          pgdg96                       565 k
 postgresql96-devel                         x86_64                       9.6.5-1PGDG.rhel7                          pgdg96                       1.8 M
 postgresql96-libs                          x86_64                       9.6.5-1PGDG.rhel7                          pgdg96                       312 k
 postgresql96-server                        x86_64                       9.6.5-1PGDG.rhel7                          pgdg96                       4.3 M

Transaction Summary
=======================================================================================================================================================
Downgrade  5 Packages

Total download size: 8.3 M
Is this ok [y/d/N]: y
Downloading packages:
(1/5): postgresql96-contrib-9.6.5-1PGDG.rhel7.x86_64.rpm                                                                        | 565 kB  00:00:04
(2/5): postgresql96-9.6.5-1PGDG.rhel7.x86_64.rpm                                                                                | 1.4 MB  00:00:05
(3/5): postgresql96-devel-9.6.5-1PGDG.rhel7.x86_64.rpm                                                                          | 1.8 MB  00:00:01
(4/5): postgresql96-libs-9.6.5-1PGDG.rhel7.x86_64.rpm                                                                           | 312 kB  00:00:00
(5/5): postgresql96-server-9.6.5-1PGDG.rhel7.x86_64.rpm                                                                         | 4.3 MB  00:00:02
-------------------------------------------------------------------------------------------------------------------------------------------------------
Total                                                                                                                  1.0 MB/s | 8.3 MB  00:00:08
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : postgresql96-libs-9.6.5-1PGDG.rhel7.x86_64                                                                                         1/10
  Installing : postgresql96-9.6.5-1PGDG.rhel7.x86_64                                                                                              2/10
  Installing : postgresql96-devel-9.6.5-1PGDG.rhel7.x86_64                                                                                        3/10
  Installing : postgresql96-server-9.6.5-1PGDG.rhel7.x86_64                                                                                       4/10
  Installing : postgresql96-contrib-9.6.5-1PGDG.rhel7.x86_64                                                                                      5/10
  Cleanup    : postgresql96-contrib-9.6.6-1PGDG.rhel7.x86_64                                                                                      6/10
  Cleanup    : postgresql96-server-9.6.6-1PGDG.rhel7.x86_64                                                                                       7/10
  Cleanup    : postgresql96-devel-9.6.6-1PGDG.rhel7.x86_64                                                                                        8/10
  Cleanup    : postgresql96-9.6.6-1PGDG.rhel7.x86_64                                                                                              9/10
  Cleanup    : postgresql96-libs-9.6.6-1PGDG.rhel7.x86_64                                                                                        10/10
  Verifying  : postgresql96-devel-9.6.5-1PGDG.rhel7.x86_64                                                                                        1/10
  Verifying  : postgresql96-server-9.6.5-1PGDG.rhel7.x86_64                                                                                       2/10
  Verifying  : postgresql96-libs-9.6.5-1PGDG.rhel7.x86_64                                                                                         3/10
  Verifying  : postgresql96-contrib-9.6.5-1PGDG.rhel7.x86_64                                                                                      4/10
  Verifying  : postgresql96-9.6.5-1PGDG.rhel7.x86_64                                                                                              5/10
  Verifying  : postgresql96-devel-9.6.6-1PGDG.rhel7.x86_64                                                                                        6/10
  Verifying  : postgresql96-contrib-9.6.6-1PGDG.rhel7.x86_64                                                                                      7/10
  Verifying  : postgresql96-server-9.6.6-1PGDG.rhel7.x86_64                                                                                       8/10
  Verifying  : postgresql96-libs-9.6.6-1PGDG.rhel7.x86_64                                                                                         9/10
  Verifying  : postgresql96-9.6.6-1PGDG.rhel7.x86_64                                                                                             10/10

Removed:
  postgresql96.x86_64 0:9.6.6-1PGDG.rhel7         postgresql96-contrib.x86_64 0:9.6.6-1PGDG.rhel7    postgresql96-devel.x86_64 0:9.6.6-1PGDG.rhel7
  postgresql96-libs.x86_64 0:9.6.6-1PGDG.rhel7    postgresql96-server.x86_64 0:9.6.6-1PGDG.rhel7

Installed:
  postgresql96.x86_64 0:9.6.5-1PGDG.rhel7         postgresql96-contrib.x86_64 0:9.6.5-1PGDG.rhel7    postgresql96-devel.x86_64 0:9.6.5-1PGDG.rhel7
  postgresql96-libs.x86_64 0:9.6.5-1PGDG.rhel7    postgresql96-server.x86_64 0:9.6.5-1PGDG.rhel7

Complete!

```
As mentioned in the YUM log, the packages were removed and then were installed again with the specific version. Hope this could help you.

Ok, that's all folks.


Image of [elephant](https://pixabay.com/es/elefante-dibujo-l%C3%A1piz-animal-831362/)