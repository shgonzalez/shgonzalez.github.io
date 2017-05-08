---
layout: post
categories: Linux LDAP ActiveDirectory
---

In this tutorial I going to show you how to setup a LDAP Directory working with Active Directory which holds the username and password but the LDAP Directory holds
the groups and authorizations in the Linux Domain.

#### In this setup I am going to cover:

* 2 389-Directory Server on centos 7
* The 2 servers above as members of the Windows Domain and enabling the PAM Passthrough Plugin 
* Replication Master Single between Directory Servers

#### The goal

![ldapscheme][goal]

[goal]: https://www.draw.io/?lightbox=1&highlight=0000ff&nav=1&title=ldap%20network%20scheme#R7ZlNb9s4EIZ%2FjY81KFKS5WPixN1DCgTrw3ZPC1qiJW5ojUFRsb2%2FvqREWZ9O1FZYo0jVQ62X3%2FPMDDHKjKz2p8%2BSHpIvEDExwyg6zcjDDGMcLLD%2BzyjnUnEwIaUSSx5ZrRY2%2FD9mRWTVnEcsa3VUAELxQ1sMIU1ZqFoalRKO7W47EO1VDzS2K6Ja2IRUsF63v3ikEqs6%2FrJu%2BIPxOLFLB9gvG7Y0fIkl5Kldb4bJrnjK5j2t5rLrZgmN4NiQyOOMrCSAKn%2FtTysmjHErs5Xj1ldaL%2FuWLFVjBizsiFcqcnv2J57mJy2tBNeTZHaf6lzZpjgdM%2BOdGbk%2FJlyxzYGGpvWo3UFridoL27zjQqxAgCzGkshjQeRqncrQUtcbJPevTCquzX8neJxqUYGZJ1MSXlhjeIC3xPd1S%2F%2BY1Tn0ROzUkOyxPzPYMyXPuott%2FeR4gYVgvfST61q8x5o5dm2npIHbcaw7U%2Btn8WX62tb6hzX3FdO7PdPfhYq%2FMq09cKndGor9PsCe8nRSDCv9rNcDdt%2BCUrA3fKwgy0M3ePlDYPwwYNudbokljYzbVG0ppMwMqNzcmQrewm3DI6iH7sK3hc5DP4%2FO70fN5pyGiYQUch0x6E92ENquioMGh77QTDGpf2x4Gmt%2B11GiNrZ%2FmVJna3eaK9ASSJVADCkVT2CCpOjH0ujOZD1DUED4UkprLqqJIpolF18p6VV5jQzxRGi9RugtVhnkMrT792zYKCpjZrv51swsitkoomiOnAC3A9IJynfJBC0io5XnBwDa6Z%2BB6%2B3WXWC3y%2FTGuoQvuxgFvTplA7qI6MGZZ0zGHOaCbntkq%2Fh6olsmniHjhUc04yw7lPfXjp8MoCb9MUmxjq%2BDWXJ%2Fis2NPA%2B5kvw0V3Bk8h%2B9Pz3VVIHn%2B%2B3Ac%2FuB53v9uAu8CcIODRLAH4wAwbcjQHoGNgG%2Bsa91cnqs1fur6a2RtqwV66yFviP9DaQv8wymulEJzcf9hFaF%2F7sJbXSyGm1098MaffQtMr3R%2FWmNrpf%2FavS5u3Qr4e9CCKrXZya53qdJFMX4X4uUdzNSePBW%2BGCXgrvsFDMO%2Fv%2BuhcXb14K1xi%2BYkry%2Bo1cV8w0c3es5es%2Fsb9d8q9Vg8RYEzj25XrxVclmUlpGSGgA9w%2BIBn0%2BZOoJ8yeahgNyE0g7qFerPI1PV9KhTQmC%2FX9MvB%2BrCScrCxfuALpUwapZlnYAYTCYdnh4y%2F7QuOkks1PYr8sq7We5KJusA1MUTD9m8TFcGY26q2YnTl%2Bd2uLkOmZPms%2BhhrL7WtDAuJ8AY%2FMb4o7dQt4K%2FJcZ%2Bvf4b40iM%2BGYY9Wv99bv8SlP%2FjYE8fgM%3D

#### The environment:

* One Active Directory called ad.sergio.lab
* Workgroup: SERGIO




#### First step, configure the LDAP as a member of Windows Domain
This is because the our LDAP will transfer the username and the password to the Active Directory which validates both.
To get this, we need to configure the sssd service.

Make sure the clocks on the LDAP server and Active Directory must be sync for kerberos to work properly.

Configure the DNS in the LDAP server with your Active Directory IP address. In my case my domain is sergio.lab and the AD ip is 10.0.2.6

```
cat /etc/resolv.conf 
search sergio.lab
nameserver 10.0.2.6
```

test ping your domain

```
ping sergio.lab
```

Now install the following packages 

```
yum install krb5-workstation samba-common-tools sssd-ad
```


now set up Kerberos as follow

```
vim /etc/krb5.conf
```

```
[logging]
 default = FILE:/var/log/krb5libs.log

[libdefaults]
 default_realm = SERGIO.LAB
 dns_lookup_realm = true
 dns_lookup_kdc = true
 ticket_lifetime = 24h
 renew_lifetime = 7d
 rdns = false
 forwardable = yes
```
 
 Now configure the SAMBA server
``` 
 vim /etc/samba/smb.conf
 ```
 
 ```
 [global]
   workgroup = SERGIO
   client signing = yes
   client use spnego = yes
   kerberos method = secrets and keytab
   log file = /var/log/samba/%m.log
   password server = AD.SERGIO.LAB
   realm = SERGIO.LAB
   security = ads
 ```  
  
In case you have multiple Active Directory, add as follow

```
 [global]
   workgroup = SERGIO
   client signing = yes
   client use spnego = yes
   kerberos method = secrets and keytab
   log file = /var/log/samba/%m.log
   password server = AD.SERGIO.LAB, AD2.SERGIO.LAB, *
   realm = SERGIO.LAB
   security = ads
 
 ```
 > *note*: the asterisk represents the best option for samba to reach the AD
 
 The next step is add the Linux server to the Active Directory domain (you will need a administrative user)
 
 obtaining credentials for kerberos
 ```
 kinit Administrator
 ```
 add the server to the domain
 ```
 net ads join -k -S ad.sergio.lab -U Administrator
 ```
 this create the /etc/krb5.keytab file.
 
#### we almost done :muscle:
 
 Again, if you have multiple AD make sure the AD servers is added in the krb5.conf file as follow:
```
SERGIO.LAB = {
 KDC = AD.SERGIO.LAB
 KDC = AD2.SERGIO.LAB
} 
```

Now run the following command to enable sssd for system authentication
```
 authconfig --update --enablesssd --enablesssdauth --enablemkhomedir
 ```
 Open the sssd file
 ```
 vim /etc/sssd/sssd.conf
 ```
 ```
 [sssd]
 domains = sergio.lab
 services = nss, pam, pac
 config_file_version = 2
 
 [domain/sergio.lab]
 id_provider = ad
 auth_provider = ad
 chpass_provider = ad
 access_provider = ad
 ad_server = ad.sergio.lab
 ad_domain = sergio.lab
 cache_credentials = true
 use_fully_qualified_names = false
 default_shell = /bin/bash
 override_homedir = /home/%d/%u
 ignore_group_members = true
 ldap_referrals = false
 ```
 
 > notice ldap_referrals = false and ignore_group_members = true this can boost performance in large environments.
 
 You can add more ad_server with comma separated values:
 ```
 ad_server = ad.sergio.lab, ad2.sergio.lab
 ```
 Now restart the services:
 ```
 systemctl restart sshd.service
 systemctl restart sssd.service
 ```
 To test your setup, you can run the id command following a username of the AD domain and get user information from AD server.
 
 ```
 id Administrator
 ```
 
 so far, we have our Linux server into Windows domain.

**This step is repeated in the second Linux Server.**

In the next part of the tutorial we will install the 389 Directory Server, create a self signed certificate for clients and configure ldap clients. See you on the flipside :thumbsup:

Your thoughts and suggestions are always welcome, please feel free to comment or ask questions if you need a hand. 
