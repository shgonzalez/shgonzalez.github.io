---
layout: post
categories: Linux HA
title: Solve "invalid password" in Keepalived
date:   2018-04-13 18:00:00 -0400
comments: true
image: /assets/img/datacenter.jpg
description: How to solve the "invalid password" message in a Keepalived configuration
tags: linux HA keepalived haproxy 
published: true
---

```bash
Apr 13 10:29:44 lb1 Keepalived_vrrp: VRRP_Instance(VI_1) ignoring received advertisment...
Apr 13 10:29:45 lb1 Keepalived_vrrp: receive an invalid passwd!
Apr 13 10:29:45 lb1 Keepalived_vrrp: bogus VRRP packet received on eth0 !!!
```

This problem is due another keepalived configuration, hence is sending its VRRP message in the same virtual_router_id. Capturing vrrp packets with tcpdump you’ll see how another machine is sending messages to your configuration and filling the logs with the shown above. To solve this issue you must reset the virtual_router_id to another one.

References: [LINK](http://keepalived-devel.narkive.com/rvCxtjY7/disable-logs-like-receive-an-invalid-passwd-on-keepalived)