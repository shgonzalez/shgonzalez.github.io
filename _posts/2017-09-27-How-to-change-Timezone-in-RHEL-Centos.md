---
layout: post
categories: Linux
title: How to change timezone in RHEL based distributions - release 6 and 7
date:   2017-09-27 16:11:01 -0400
comments: true
image: /assets/img/PrimeTime.jpg
description: Setting correctly time and date in RHEL based distributions - release 6 and 7
tags: centos 7 6 oracle redhat red hat ntp time date timedatectl rhel linux 
published: true
---

This mini how-to will show you how to change the timezone in `RHEL` derived servers.

### Configuration in RHEL/CENTOS/ORACLE LINUX release 6.

1. List time zones
```bash
ls /usr/share/zoneinfo
```
2. Change the Time Zone. In the graphical mode you can set up the time zone using `system-config-date` command.
```bash
sudo mv /etc/localtime /etc/localtime.bkp 
sudo ln -s /usr/share/zoneinfo/America/Asuncion /etc/localtime
sudo vim /etc/sysconfig/clock
```
```
[sergio@localhost ~]$ sudo cat /etc/sysconfig/clock
# The time zone of the system is defined by the contents of /etc/localtime.
# This file is only for evaluation by system-config-date, do not rely on its
# contents elsewhere.
ZONE="America/Asuncion"
```



3. View when clock will change forward or backward
```
[sergio@localhost ~]$ zdump -v /etc/localtime | grep 2018
/etc/localtime  Sun Mar 25 02:59:59 2018 UTC = Sat Mar 24 23:59:59 2018 PYST isdst=1 gmtoff=-10800
/etc/localtime  Sun Mar 25 03:00:00 2018 UTC = Sat Mar 24 23:00:00 2018 PYT isdst=0 gmtoff=-14400
/etc/localtime  Sun Oct  7 03:59:59 2018 UTC = Sat Oct  6 23:59:59 2018 PYT isdst=0 gmtoff=-14400
/etc/localtime  Sun Oct  7 04:00:00 2018 UTC = Sun Oct  7 01:00:00 2018 PYST isdst=1 gmtoff=-10800
```
4. If you need to use a particular Timezone in your environment you can easily use the TZ variable and set you time zone.
```
[sergio@localhost ~]$ date; export TZ="America/Argentina/Ushuaia"
Wed Sep 27 18:19:51 PYT 2017
[sergio@localhost ~]$ date
Wed Sep 27 19:19:55 ART 2017
```

### Configuration in RHEL/CENTOS/ORACLE LINUX release 7.

1. You can list the Time zone using timedatectl command as follow
```bash
timedatectl list-timezones
```
Or search the Timezone interactibly with the tzselect command

2. Change the Time zone.
```bash
timedatectl set-timezone America/Asuncion
```
3. Verify the changes and the dates when clock will change.
```
[root@loadbalancer ~]# timedatectl
      Local time: mié 2017-09-27 19:22:59 -04
  Universal time: mié 2017-09-27 23:22:59 UTC
        RTC time: mié 2017-09-27 23:22:58
       Time zone: America/Asuncion (-04, -0400)
     NTP enabled: n/a
NTP synchronized: no
 RTC in local TZ: no
      DST active: no
 Last DST change: DST ended at
                  sáb 2017-03-25 23:59:59 -03
                  sáb 2017-03-25 23:00:00 -04
 Next DST change: DST begins (the clock jumps one hour forward) at
                  sáb 2017-09-30 23:59:59 -04
                  dom 2017-10-01 01:00:00 -03
```

4. You can use zdump to view when the clock will change.
```
[root@loadbalancer ~]# zdump -v /etc/localtime | grep 2018
zdump: atención: zona "/etc/localtime" abreviatura "-04" no tiene caracteres alfabéticos al comienzo
/etc/localtime  Sun Mar 25 02:59:59 2018 UTC = Sat Mar 24 23:59:59 2018 -03 isdst=1 gmtoff=-10800
/etc/localtime  Sun Mar 25 03:00:00 2018 UTC = Sat Mar 24 23:00:00 2018 -04 isdst=0 gmtoff=-14400
/etc/localtime  Sun Oct  7 03:59:59 2018 UTC = Sat Oct  6 23:59:59 2018 -04 isdst=0 gmtoff=-14400
/etc/localtime  Sun Oct  7 04:00:00 2018 UTC = Sun Oct  7 01:00:00 2018 -03 isdst=1 gmtoff=-10800
```

5. If you need to use a particular Time zone in your environment you can easily use the TZ variable and set you time zone.
```
[sergio@localhost ~]$ date; export TZ="America/Argentina/Ushuaia"
Wed Sep 27 18:19:51 PYT 2017
[sergio@localhost ~]$ date
Wed Sep 27 19:19:55 ART 2017
```
--- 

Sometimes the goverment change the Daylight Saving Time or DST. To achieve this accomplish you can compile a new time zone using the zic command. First download the [IANA TIME ZONE DATABASE](https://www.iana.org/time-zones), this is because it contains the code and data that represent the history of localtime for many locations.
```bash
wget https://www.iana.org/time-zones/repository/releases/tzdata2017b.tar.gz
```
After extract the data you can use it as reference for your new file.
In my case this is only dummy file.
```bash
vim test.zic
```
```bash
#Rule  NAME  FROM  TO    TYPE  IN   ON       AT    SAVE  LETTER/S
Rule  test    2017  only  -     Apr  Sun>=1  0:00   1:00  S
Rule  test    2017  only  -     Oct  Sun>=1  0:00   0  -
#Zone  NAME                GMTOFF  RULES/SAVE  FORMAT  [UNTIL]
#
Zone America/test      -4:00 test      TS%sT
```
```bash
sudo zic test.zic
ls -l /usr/share/zoneinfo/America/test
```
After that you can use the zdump command as shown above to verify when the clock will change.

