---
layout: post
categories: Storage Windows 
title: Identify a LUN disk in Windows Server 
date:   2017-06-23 09:00:01 -0400
comments: true
image: /assets/img/resizepartition2.png
description: How to identify a storage LUN in Windows Server
tags: storage san windows server 
published: false
---

Mapping the Logical Unit Number in Windows can be difficult when you have lots of disks, in a SAN environment you may need resize a LUN or delete one. With this simple command you can identify a LUN in Windows Server.

First, we need the disk number, issue this command in the "Run" box: diskmgmt.msc

Once identified the disk, run diskpart as Administrator

select the disk number

Now execute disk detail


Some storage vendors have they utility tool, in this example I am going to use the utility from EMC (powerpath) and Hitachi (Dynamic Link Manager).

