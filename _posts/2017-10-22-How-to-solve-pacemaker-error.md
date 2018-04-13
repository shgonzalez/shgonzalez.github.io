---
layout: post
categories: Linux HA
title: How to solve "Unable to communicate" error in Pacemaker
date:   2017-10-2 20:00:01 -0300
comments: true
image: /assets/img/Pacemaker.png
description: Solving errors when creating clusters with Pacemaker
tags: pacemaker ha high availability linux centos 7 redhat 7 
published: true
---


The messages after issue the command "pcs cluster auth".

```bash
pcs cluster auth node1.sergio.lab node2.sergio.lab -u hacluster -p 9b56d5571925da03c7618cafa6c923f68307a90f --debug
```

```bash
Running: /usr/bin/ruby -I/usr/lib/pcsd/ /usr/lib/pcsd/pcsd-cli.rb auth
--Debug Input Start--
{"username": "hacluster", "local": false, "nodes": ["node2.sergio.lab", "node1.sergio.lab"], "password": "9b56d5571925da03c7618cafa6c923f68307a90f", "force": false}
--Debug Input End--
Return Value: 0
--Debug Output Start--
{
  "status": "ok",
  "data": {
    "auth_responses": {
      "node1.sergio.lab": {
        "status": "noresponse"
      },
      "node2.sergio.lab": {
        "status": "noresponse"
      }
    },
    "sync_successful": true,
    "sync_nodes_err": [

    ],
    "sync_responses": {
    }
  },
  "log": [
    "I, [2017-05-05T18:22:55.180050 #37871]  INFO -- : PCSD Debugging enabled\n",
    "D, [2017-05-05T18:22:55.180166 #37871] DEBUG -- : Did not detect RHEL 6\n",
    "I, [2017-05-05T18:22:55.180235 #37871]  INFO -- : Running: /usr/sbin/corosync-cmapctl totem.cluster_name\n",
    "I, [2017-05-05T18:22:55.180298 #37871]  INFO -- : CIB USER: hacluster, groups: \n",
    "D, [2017-05-05T18:22:55.183060 #37871] DEBUG -- : []\n",
    "D, [2017-05-05T18:22:55.183135 #37871] DEBUG -- : [\"Failed to initialize the cmap API. Error CS_ERR_LIBRARY\\n\"]\n",
    "D, [2017-05-05T18:22:55.183194 #37871] DEBUG -- : Duration: 0.002736672s\n",
    "I, [2017-05-05T18:22:55.183261 #37871]  INFO -- : Return Value: 1\n",
    "W, [2017-05-05T18:22:55.183314 #37871]  WARN -- : Cannot read config 'corosync.conf' from '/etc/corosync/corosync.conf': No such file\n",
    "W, [2017-05-05T18:22:55.183385 #37871]  WARN -- : Cannot read config 'corosync.conf' from '/etc/corosync/corosync.conf': No such file or directory - /etc/corosync/corosync.conf\n",
    "I, [2017-05-05T18:22:55.184065 #37871]  INFO -- : SRWT Node: node2.sergio.lab Request: check_auth\n",
    "E, [2017-05-05T18:22:55.184103 #37871] ERROR -- : Unable to connect to node node2.sergio.lab, no token available\n",
    "I, [2017-05-05T18:22:55.184171 #37871]  INFO -- : SRWT Node: node1.sergio.lab Request: check_auth\n",
    "E, [2017-05-05T18:22:55.184214 #37871] ERROR -- : Unable to connect to node node1.sergio.lab, no token available\n",
    "I, [2017-05-05T18:22:55.186666 #37871]  INFO -- : No response from: node1.sergio.lab request: /auth, exception: getaddrinfo: Name or service not known\n",
    "I, [2017-05-05T18:22:55.186780 #37871]  INFO -- : No response from: node2.sergio.lab request: /auth, exception: getaddrinfo: Name or service not known\n"
  ]
}
--Debug Output End--

Error: Unable to communicate with node2.sergio.lab
Error: Unable to communicate with node1.sergio.lab
```
This debug message `Failed to initialize the cmap API. Error CS_ERR_LIBRARY` indicates you don't have any cluster running yet. [Link](https://github.com/ClusterLabs/pcs/issues/73#issuecomment-170903946)

To solve this error you must simply unset the `http_proxy` and `https_proxy` variables. After that you are able to create the cluster.

```bash
unset http_proxy && unset https_proxy
```


