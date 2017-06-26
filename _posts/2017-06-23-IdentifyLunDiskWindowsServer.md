---
layout: post
categories: Storage Windows 
title: Identify a LUN disk in Windows Server 
date:   2017-06-23 09:00:01 -0400
comments: true
image: /assets/img/datacenter.jpg
description: How to identify a storage LUN in Windows Server
tags: storage san windows server 
published: true
---

Mapping the Logical Unit Number in Windows can be difficult when you have lots of disks, in a SAN environment you may need resize a LUN or delete one. With these simple steps you can identify a LUN disk in Windows Server.

First, we need the disk number, issue this command in the "Run" box: `diskmgmt.msc`

![run][run]

[run]: /assets/img/run.png


![diskmgt][diskmgt]

[diskmgt]: /assets/img/diskmgt.png


Once identified the disk, run `diskpart` as Administrator

Select the disk number and execute detail disk

![diskpart][diskpart]

[diskpart]: /assets/img/diskpart.png


The `LUN ID` is the number assigned by the storage device to the server. You can search this number and match it with the real Volume Number in your storage system.

### Vendor utilities

Some storage vendors have their own utility tool, in this example I am going to use the utility from **EMC** (powerpath) and **Hitachi** (Dynamic Link Manager).

#### PowerPath 
This utility is provided by EMC Storage.

![powermt][powermt]

[powermt]: /assets/img/powermt.png

The field Logical Device indicates the vendor and serial number of the storage.

#### Dynamic Link Manager.
This utility is provided by HDS.

```batch
dlnkmgr view -lu -item all
```

And this is the output:
```batch
Product       : V_Gx0
SerialNumber  : 440          
LUs           : 5

iLU    SLPR HDevName PathID PathName                        ChaPort CLPR Status     Type IO-Count   IO-Errors  DNum IEP
000230    - K        000000 0002.0000.0000000000000000.0000 2C         0 Online     Own      243515          0    0 -  
                     000005 0003.0000.0000000000000000.0000 1C         0 Online     Own      211708          0    0 -  
000242    - H        000001 0002.0000.0000000000000000.0001 2C         0 Online     Own   562393768          0    0 -  
                     000006 0003.0000.0000000000000000.0001 1C         0 Online     Own   563847170          0    0 -  
000243    - -        000002 0002.0000.0000000000000000.0002 2C         0 Online     Own       30136          0    0 -  
                     000007 0003.0000.0000000000000000.0002 1C         0 Online     Own           0          0    0 -  
0002EF    - Q        000003 0002.0000.0000000000000000.0003 2C         0 Online     Own     5453717          0    0 -  
                     000008 0003.0000.0000000000000000.0003 1C         0 Online     Own     5413974          0    0 -  
000461    - I        000004 0002.0000.0000000000000000.0004 2C         0 Online     Own   634021097          0    0 -  
                     000009 0003.0000.0000000000000000.0004 1C         0 Online     Own   630819732          0    0 -  
```

The four last numbers in PathName (for example, 0002) represents the LUN ID and iLU number represents the Volume itself in Hitachi storage.

Your thoughts and suggestions are always welcome, please feel free to comment or ask questions if you need a hand. 



