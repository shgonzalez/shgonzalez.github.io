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

[goal]: /assets/img/ldapscheme.jpg

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
 
 > notice ldap_referrals = false and ignore_group_members = true this can boost performance in large environments. Please check /etc/sssd/sssd.conf have "root" as owner and group, and 600 file permissions
 
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
