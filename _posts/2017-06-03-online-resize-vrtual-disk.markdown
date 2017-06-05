---
layout: post
categories: Linux Vmware
title: Online resize virtual disk 
date:   2017-06-03 09:00:01 -0400
comments: true
image: /assets/img/hplogo.jpg
description: How to resize virtual disk on vmware with 0 downtime using Centos 7 and PostgreSQL 9.6.
tags: centos redhat linux virtual disk vmdk vmware postgresql rhel 
published: false
---
### Intro
Sometimes (Always) you need resize a partition in a LVM (Logical Volume Manager) but the common solution is adding a new disk to VG (Volume Group). That's OK but in the next time you will need a new resize and then new resize and so on, and you will have lots of disks per Virtual Machine and that is very difficult to handle when you need to delete some disk for example. The best approach is to take advantage of virtualization. In this guide I am going to show you how to resize a virtual disk which is using by PostgreSQL and simulate a load with a mini sql insert sentence.

Warning, if you not follow this guide as decribed may result in lost of data, please be sure to practice first in a lab environment before doing it in a production environment.


