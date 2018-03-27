---
layout: post
categories: HPUX
title: Network Commands in HPUX
date:   2018-03-15 09:53:00 -0300
comments: true
image: /assets/img/networkswitch.jpg
description: Sorts of commands to debug network issues in HPUX
tags: HPUX network net port aggregation apa
published: true
---
![networkswitch][networkswitch]

[networkswitch]: /assets/img/networkswitch.jpg

### Display IP addresses

```
netstat -in
```
or
```
ifconfig lan900
```

### Display routes
```
netstat -rn
```

### Configure IP, gateway and hostname
```
#vi /etc/rc.config.d/netconf
```
```
HOSTNAME="production-db"
OPERATING_SYSTEM=HP-UX
LOOPBACK_ADDRESS=127.0.0.1

ROUTE_DESTINATION[0]=default
ROUTE_MASK[0]=
ROUTE_GATEWAY[0]=10.10.1.1
ROUTE_COUNT[0]=1
ROUTE_ARGS[0]=""
ROUTE_SOURCE[0]=""

INTERFACE_NAME[0]="lan900"
IP_ADDRESS[0]="10.10.1.38"
SUBNET_MASK[0]="255.255.255.0"
BROADCAST_ADDRESS[0]=""
INTERFACE_STATE[0]=""
DHCP_ENABLE[0]=0
INTERFACE_MODULES[0]=""
```

### Restart network service
```
/sbin/init.d/net <stop|start>
```

### HP Auto Port Aggregation files
#### Configuration values for HP Auto-Port Aggregation interfaces
* /etc/rc.config.d/hp_apaconf 

#### Configuration values for Physical Ports for HP Auto-Port Aggregation interfaces
* /etc/rc.config.d/hp_apaportconf

### View the MTU size of lan900
```
[root@production-db:/]# lanadmin -m 900
MTU Size                        = 1500
```

### View the status of APA subsystem
```
nwmgr -S apa
```
### Stop/Start HP APA
```
/sbin/init.d/hplm stop
/sbin/init.d/hpapa stop
#
/sbin/init.d/hpapa start
/sbin/init.d/hplm start
```

### Check the current values of HP APA interface
```
nwmgr -v -c lan900 -S apa
```
```
lan900 current values:
   Speed = 2 Gbps Full Duplex
   MTU = 1500
   Virtual Maximum Transmission Unit = 0
   MAC Address = 0x00215a797f47
   Network Management ID = 17
   Features = Linkagg Interface
              IPV4 Recv CKO
              IPV4 Send CKO
              VLAN Support
              VLAN Tag Offload
              64Bit MIB Support
   Load Distribution Algorithm = LB_MAC
   Mode = LACP_AUTO
   Parent PPA =  -
   APA State = Up
   Membership = 5,1
   Active Port(s) = 5,1
   Not Ready Port(s) =  -
   Key = 900
   Operational Key = 0
   Fixed Mac Address = off
```

### Change MTU size
```
nwmgr -s -f -c lan900 -A mtu=1500 --cu
```
#### Save the new MTU size
```
nwmgr -s -A all --sa --fr cu -c lan900
```


### Check processes associated by a port, for example port tcp:22
```
 lsof -i tcp:22
```
### Verify the switch which is connected with CDP  (Cisco Discovery Protocol) 
```
tcpdump -nn -v -i lan900 -s 1500 -c 1 'ether[20:2] == 0x2000' 
```

### Restart ssh service
```
/sbin/init.d/secsh <stop|start>
```