---
layout: post
categories: Linux LDAP ActiveDirectory
title: "LDAP + Active Directory Configuration Part 3"
date:   2017-05-19 08:00:01 -0400
comments: true
image: /assets/img/ldapscheme.jpg
description: In this tutorial I going to show you how to setup a LDAP Directory working with Active Directory which holds the username and password but the LDAP Directory holds the groups and authorizations in the Linux Domain.
---

In this section we are going to cover:
* Activate Plugin  PAM Passthrough.
* HAProxy configuration for load balancing.
* Configure LDAP clients.

In the [first section]({{ site.baseurl }}{% post_url 2017-05-02-LDAP-+-Active-Directory-Configuration-Part-I %}) we covered how to add Linux servers into a Windows Domain, in the [second section]({{ site.baseurl }}{% post_url 2017-05-08-LDAP-+-Active-Directory-Configuration-Part-II %}) we configured 2 389 Directory Server with replication.
Now we are going to activate the Plugin  PAM Passthrough, with this feature our clients authenticates using the Active Directory Domain password and LDAP server provides userID and GroupID.


### Activate Plugin  PAM Passthrough
First, we need to activate the plugin.
```
ldapmodify  -D "cn=Directory Manager" -h localhost -p 389  -W
Enter LDAP Password:
dn: cn=PAM Pass Through Auth,cn=plugins,cn=config
changetype: modify
replace: nsslapd-pluginEnabled
nsslapd-pluginEnabled: on

modifying entry "cn=PAM Pass Through Auth,cn=plugins,cn=config"
```

Then we create the next file indicating values for the entry.
```
vim pam_plugin.ldif

# extended LDIF
#
# LDAPv3
# base <cn=PAM Pass Through Auth,cn=plugins,cn=config> with scope subtree
# filter: (objectclass=*)
# requesting: ALL
#

# PAM Pass Through Auth, plugins, config
dn: cn=PAM Pass Through Auth,cn=plugins,cn=config
changetype: Modify
add: pamExcludeSuffix
pamExcludeSuffix: o=NetscapeRoot
-
delete: pamIDMapMethod
-
delete: pamIDAttr
-
replace: pamService
pamservice: system-auth
-
```

Now execute the ldapmomdify as follow:
```
ldapmodify -x -D "cn=Directory Manager" -h localhost -p 389  -W  -f pam_plugin.ldif -S errors.txt
```

Now restart the service
```
systemctl restart dirsrv.target
```



Now you can query the entry and must look like this:
```
ldapsearch -x -D "cn=Directory Manager" -h localhost -p 389 -b "cn=PAM Pass Through Auth,cn=plugins,cn=config" -W
Enter LDAP Password:
# extended LDIF
#
# LDAPv3
# base <cn=PAM Pass Through Auth,cn=plugins,cn=config> with scope subtree
# filter: (objectclass=*)
# requesting: ALL
#

# PAM Pass Through Auth, plugins, config
dn: cn=PAM Pass Through Auth,cn=plugins,cn=config
objectClass: top
objectClass: nsSlapdPlugin
objectClass: extensibleObject
objectClass: pamConfig
cn: PAM Pass Through Auth
nsslapd-pluginPath: libpam-passthru-plugin
nsslapd-pluginInitfunc: pam_passthruauth_init
nsslapd-pluginType: betxnpreoperation
nsslapd-pluginEnabled: on
nsslapd-pluginloadglobal: true
nsslapd-plugin-depends-on-type: database
pamMissingSuffix: ALLOW
pamExcludeSuffix: cn=config
pamExcludeSuffix: o=NetscapeRoot
pamFallback: FALSE
pamSecure: TRUE
pamService: system-auth
nsslapd-pluginId: pam_passthruauth
nsslapd-pluginVersion: 1.3.5.10
nsslapd-pluginVendor: 389 Project
nsslapd-pluginDescription: PAM pass through authentication plugin

# search result
search: 2
result: 0 Success

# numResponses: 2
# numEntries: 1

```

**Repeat these steps in the second server ds2.sergio.lab**


### HAProxy configuration for load balancing

To achieve our goal we use haproxy. Also this server will host the CA Certificate, thus our clients can download it. In this section we are going to work on server ldap.sergio.lab. 
```
yum -y install haproxy openssl-devel httpd
```
now we need our CA Certificate (cacert.pem) for verification. Copy that file to ldap.sergio.lab
```
 [root@ldap ~]# scp -p root@ds1:/etc/dirsrv/slapd-ds1/cacert.pem /etc/haproxy/
 
 chmod 640 /etc/haproxy/cacert.pem
 ```
 Open your firewall for ldap, ldaps and http
```
firewall-cmd --permanent --add-service=ldap
firewall-cmd --permanent --add-service=ldaps
firewall-cmd --permanent --add-service=http
firewall-cmd --reload
```

Add the following to */etc/haproxy/haproxy.cfg* file
```
listen stats 0.0.0.0:8000
        mode http
        stats enable
        stats uri /stats
        stats auth haproxy:haproxy

frontend ldap
        mode tcp
        bind 0.0.0.0:389
        default_backend ldapbackend


backend ldapbackend
        mode tcp
        balance roundrobin
        option ldap-check
        server ds1 ds1.sergio.lab:389 check port 389
        server ds2 ds2.sergio.lab:389 check port 389

frontend ldaps
        mode tcp
        bind 0.0.0.0:636
        default_backend ldapsbackend

backend ldapsbackend
        mode tcp
        balance roundrobin
        server ds1 ds1.sergio.lab:636 check ssl verify required ca-file /etc/haproxy/cacert.pem
        server ds2 ds2.sergio.lab:636 check ssl verify required ca-file /etc/haproxy/cacert.pem


```
		
Now copy the cacert.pem file to /var/www/html/
```
cp cacert.pem /var/www/html/
```
Verify the selinux context.
```
[root@ldap ~]# ls -lZ /var/www/html/
-rw-r--r--. root root unconfined_u:object_r:httpd_sys_content_t:s0 cacert.pem
```

Now activate the Selinux boolean, with this option haproxy can bind ports
```
setsebool -P haproxy_connect_any=1
```
After that we are able to activate the services in ldap.sergio.lab
```
systemctl enable haproxy httpd
systemctl start haproxy httpd
```

### Configure LDAP clients
Now we are able to configure linux clients with LDAP. In my case I use a test server.

Run the following command.
```
yum install openldap-clients nss-pam-ldapd sssd -y
```
```
authconfig --enableshadow --enableldap --enableldapauth --ldapserver=ldap.sergio.lab --ldapbasedn="dc=sergio,dc=lab" --enableldaptls --enablesssd --enablesssdauth --ldaploadcacert=http://ldap.sergio.lab/cacert.pem --enablelocauthorize  --enablemkhomedir --disablefingerprint --update
```
You can check retrieving ldap users with getent and doing ssh
```
getent passwd sgonzalez
sgonzalez:*:2100:2100:Sergio H. Gonzalez:/home/sgonzalez:/bin/bash
```
```
ssh -l sgonzalez localhost
```



### Conclusion
We met our goal indicated in [part 1]({{ site.baseurl }}{% post_url 2017-05-02-LDAP-+-Active-Directory-Configuration-Part-I %}), now our Linux Clients are authenticated with LDAP and using the password from Active Directory. This is useful in environments with Linux and Windows Servers because is cumbersome managing both passwords.
Another option to this approach is [Windows Sync](https://access.redhat.com/documentation/en-us/red_hat_directory_server/10/html/administration_guide/windows_sync) but this implies install some components on Active Directory.


I hope this will help you. See you next.  




References:
* [haproxy](https://cbonte.github.io/haproxy-dconv/configuration-1.5.html)
* man authconfig
* man sssd

Your thoughts and suggestions are always welcome, please feel free to comment or ask questions if you need a hand. 