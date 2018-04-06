---
layout: post
categories: Linux
title: SSH key authentication failed
date:   2018-04-06 18:04:00 -0400
comments: true
image: /assets/img/keylock.png
description: A checklist to verify when authentication with ssh keys not working
tags: ssh keys linux authentication
published: true
---
![keylock.png][keylock.png]

[keylock.png]: /assets/img/keylock.png


####  This is a checklist to solve authentication with ssh keys :key: if it doesn't working 

* Check logs, especially the auth log or secure log

```
Feb  2 10:27:46 myhost sshd[37018]: Authentication refused: bad ownership or modes for directory /home/sgonzalez
Feb  2 10:27:51 myhost sshd[37018]: Accepted password for sgonzalez from 10.198.33.99 port 42818 ssh2
```

As shown above, this is solved running `chmod 700 /home/sgonzalez`

* Verify permissions of `~/.ssh/authorized_keys` must be `0700` 
```
-rw------- 1 sgonzalez sgonzalez   735 Jul  6  2015 authorized_keys
```

* The Security Enhanced Linux (SELinux). Check if your home directory is correctly labeled. You can run the following command to reset the label.

```
restorecon -Rv /home/sgonzalez
```


So far, you may solve the authentication with ssh keys :unlock:. If not, I'm glad to help you so leave me a comment and I am going to answer. 

