---
layout: post
categories: Linux LDAP HPUX
title: "LDAP Client Configuration in HP-UX"
date:   2017-06-01 13:00:01 -0400
comments: true
image: /assets/img/hplogo.jpg
description: How to configure your HPUX server to authenticate against 389 Directory Server 
---

In this time we are going to set up a new schema in our 389 Directory Server allowing HP UX clients. 
In HP UX we need a new schema called DUA (Directory User Agents) Config File, this schema has attributes needed for HP UX clients to permit authentication. By default, this schema is not configured in 389 Directory Server and we need import it using ldapmodify.

Next, an example of DUA Directory User Agents Scheme
```
dn: cn=schema
changetype: modify
add: attributetypes
attributeTypes: ( 1.3.6.1.4.1.11.1.3.1.1.0 NAME 'defaultServerList'
DESC 'List of default servers'
EQUALITY caseIgnoreMatch
SUBSTR caseIgnoreSubstringsMatch
SYNTAX 1.3.6.1.4.1.1466.115.121.1.15
SINGLE-VALUE )
dn: cn=schema
changetype: modify
add: attributetypes
attributeTypes: ( 1.3.6.1.4.1.11.1.3.1.1.1 NAME 'defaultSearchBase'
DESC 'Default base for searches'
EQUALITY distinguishedNameMatch
SYNTAX 1.3.6.1.4.1.1466.115.121.1.12
SINGLE-VALUE )
dn: cn=schema
changetype: modify
add: attributetypes
attributeTypes: ( 1.3.6.1.4.1.11.1.3.1.1.2 NAME 'preferredServerList'
DESC 'List of preferred servers'
EQUALITY caseIgnoreMatch
SUBSTR caseIgnoreSubstringsMatch
SYNTAX 1.3.6.1.4.1.1466.115.121.1.15
SINGLE-VALUE )
dn: cn=schema
changetype: modify
add: attributetypes
attributeTypes: ( 1.3.6.1.4.1.11.1.3.1.1.3 NAME 'searchTimeLimit'
DESC 'Maximum time an agent or service allows for a
search to complete'
EQUALITY integerMatch
ORDERING integerOrderingMatch
SYNTAX 1.3.6.1.4.1.1466.115.121.1.27
SINGLE-VALUE )
dn: cn=schema
changetype: modify
add: attributetypes
attributeTypes: ( 1.3.6.1.4.1.11.1.3.1.1.4 NAME 'bindTimeLimit'
DESC 'Maximum time an agent or service allows for a
bind operation to complete'
EQUALITY integerMatch
ORDERING integerOrderingMatch
SYNTAX 1.3.6.1.4.1.1466.115.121.1.27
SINGLE-VALUE )
dn: cn=schema
changetype: modify
add: attributetypes
attributeTypes: ( 1.3.6.1.4.1.11.1.3.1.1.5 NAME 'followReferrals'
DESC 'An agent or service does or should follow referrals'
EQUALITY booleanMatch
SYNTAX 1.3.6.1.4.1.1466.115.121.1.7
SINGLE-VALUE )
dn: cn=schema
changetype: modify
add: attributetypes
attributeTypes: ( 1.3.6.1.4.1.11.1.3.1.1.6 NAME 'authenticationMethod'
DESC 'Identifies the types of authentication methods either
used, required, or provided by a service or peer'
EQUALITY caseIgnoreMatch
SUBSTR caseIgnoreSubstringsMatch
SYNTAX 1.3.6.1.4.1.1466.115.121.1.15
SINGLE-VALUE )
dn: cn=schema
changetype: modify
add: attributetypes
attributeTypes: ( 1.3.6.1.4.1.11.1.3.1.1.7 NAME 'profileTTL'
DESC 'Time to live, in seconds, before a profile is
considered stale'
EQUALITY integerMatch
ORDERING integerOrderingMatch
SYNTAX 1.3.6.1.4.1.1466.115.121.1.27
SINGLE-VALUE )
dn: cn=schema
changetype: modify
add: attributetypes
attributeTypes: ( 1.3.6.1.4.1.11.1.3.1.1.9 NAME 'attributeMap'
DESC 'Attribute mappings used, required, or supported by an
agent or service'
EQUALITY caseIgnoreIA5Match
SYNTAX 1.3.6.1.4.1.1466.115.121.1.26 )
dn: cn=schema
changetype: modify
add: attributetypes
attributeTypes: ( 1.3.6.1.4.1.11.1.3.1.1.10 NAME 'credentialLevel'
DESC 'Identifies type of credentials either used, required,
or supported by an agent or service'
EQUALITY caseIgnoreIA5Match
SYNTAX 1.3.6.1.4.1.1466.115.121.1.26
SINGLE-VALUE )
dn: cn=schema
changetype: modify
add: attributetypes
attributeTypes: ( 1.3.6.1.4.1.11.1.3.1.1.11 NAME 'objectclassMap'
DESC 'Object class mappings used, required, or supported by
an agent or service'
EQUALITY caseIgnoreIA5Match
SYNTAX 1.3.6.1.4.1.1466.115.121.1.26 )
dn: cn=schema
changetype: modify
add: attributetypes
attributeTypes: ( 1.3.6.1.4.1.11.1.3.1.1.12 NAME 'defaultSearchScope'
DESC 'Default scope used when performing a search'
EQUALITY caseIgnoreIA5Match
SYNTAX 1.3.6.1.4.1.1466.115.121.1.26
SINGLE-VALUE )
dn: cn=schema
changetype: modify
add: attributetypes
attributeTypes: ( 1.3.6.1.4.1.11.1.3.1.1.13 NAME 'serviceCredentialLevel'
DESC 'Specifies the type of credentials either used, required,
or supported by a specific service'
EQUALITY caseIgnoreIA5Match
SYNTAX 1.3.6.1.4.1.1466.115.121.1.26 )
dn: cn=schema
changetype: modify
add: attributetypes
attributeTypes: ( 1.3.6.1.4.1.11.1.3.1.1.14 NAME 'serviceSearchDescriptor'
DESC 'Specifies search descriptors required, used, or
supported by a particular service or agent'
EQUALITY caseExactMatch
SUBSTR caseExactSubstringsMatch
SYNTAX 1.3.6.1.4.1.1466.115.121.1.15 )
dn: cn=schema
changetype: modify
add: attributetypes
attributeTypes: ( 1.3.6.1.4.1.11.1.3.1.1.15 NAME 'serviceAuthenticationMethod'
DESC 'Specifies types authentication methods either
used, required, or supported by a particular service'
EQUALITY caseIgnoreMatch
SUBSTR caseIgnoreSubstringsMatch
SYNTAX 1.3.6.1.4.1.1466.115.121.1.15 )
dn: cn=schema
changetype: modify
add: objectClasses
objectClasses: ( 1.3.6.1.4.1.11.1.3.1.2.5 NAME 'DUAConfigProfile'
SUP top STRUCTURAL
DESC 'Abstraction of a base configuration for a DUA'
MUST ( cn )
MAY ( defaultServerList $ preferredServerList $
defaultSearchBase $ defaultSearchScope $
searchTimeLimit $ bindTimeLimit $
credentialLevel $ authenticationMethod $
followReferrals $
serviceSearchDescriptor $ serviceCredentialLevel $
serviceAuthenticationMethod $ objectclassMap $
attributeMap $ profileTTL ) )
```
I have the above in [DUAConfigProfile.scheme.ldif file]({{ site.url}}/assets/files/DUAConfigProfile.schema.ldif) or you can search it on google.

In our Directory Server issue the following command:
```
ldapmodify -a -x -D "cn=Directory Manager" -H ldap://localhost -W -f DUAConfigProfile.scheme.ldif
```
Now you have imported the scheme.

The following step is configuring the entry. The entry must have Object Class duaconfigprofile, this is a [example of how must it looks the entry]({{ site.url}}/assets/files/duaprofile.ldif).
```
# extended LDIF
#
# LDAPv3
# base <dc=sergio,dc=lab> with scope subtree
# filter: cn=ldapuxprofile
# requesting: ALL
#

# ldapuxprofile, ldapuxprofile, sergio.lab
dn: cn=ldapuxprofile,ou=ldapuxprofile,dc=sergio,dc=lab
preferredServerList: ds1.sergio.lab:389 ds2.sergio.lab:3
 89
bindTimeLimit: 3
defaultSearchBase: dc=sergio,dc=lab
authenticationMethod: tls:simple
serviceSearchDescriptor: passwd:ou=people,dc=sergio,dc=lab?sub?(objectclass=posixaccount)
serviceSearchDescriptor: group:ou=groups,dc=sergio,dc=lab?sub?(objectclass=posixgroup)
serviceSearchDescriptor: shadow:ou=people,dc=sergio,dc=lab?sub?(objectclass=shadowaccount)
serviceSearchDescriptor: pam:ou=people,dc=sergio,dc=lab?sub?(objectclass=posixaccount)
serviceSearchDescriptor: rpc:dc=sergio,dc=lab?sub?(objectclass=oncrpc)
serviceSearchDescriptor: protocols:dc=sergio,dc=lab?sub?(objectclass=ipprotocol)
serviceSearchDescriptor: networks:dc=sergio,dc=lab?sub?(objectclass=ipnetwork)
serviceSearchDescriptor: hosts:dc=sergio,dc=lab?sub?(objectclass=iphost)
serviceSearchDescriptor: services:dc=sergio,dc=lab?sub?(objectclass=ipservice)
serviceSearchDescriptor: printers:dc=sergio,dc=lab?sub?(objectclass=printerlpr)
serviceSearchDescriptor: automount:dc=sergio,dc=lab?sub?(objectclass=automount)
serviceSearchDescriptor: netgroup:dc=sergio,dc=lab?sub?(objectclass=nisnetgroup)
cn: ldapuxprofile
objectClass: top
objectClass: duaconfigprofile
attributeMap: passwd:userpassword=*NULL*
attributeMap: shadow:userpassword=*NULL*

# search result
search: 2
result: 0 Success

# numResponses: 2
# numEntries: 1

```

Now it's time to install the CA certificate in the server using certutil. Copy your CA certificate in your hpux server, in my case is /root/cacert.pem
```
/opt/ldapux/contrib/bin/certutil -d /etc/opt/ldapux -A -n "CA-cert" -t "CT,," -a -i /root/cacert.pem
```
Now we must configure the ldap client in the server.
```
cd /opt/ldapux/config/
./setup
```
```
Select which Directory Server you want to connect to:
1. HP-UX, Red Hat or Tivoli Directory
2. Windows 2003 R2/2008 Active Directory
To accept the default shown in brackets, press the Return key.
Directory Server: [1]: ENTER
Would you like to change this configuration (Yes/No/Quit) ? [Yes]: ENTER
Directory server host : ldap.sergio.lab
Do you want to use TLS to download profile (y/n)? [y]: ENTER
Directory Server port number [389]:ENTER
Would you like to install PublicKey schema in this directory server? [Yes]:no
Profile Entry DN: []: cn=ldapuxprofile,ou=ldapuxprofile,dc=sergio,dc=lab
Press any key to continue: ENTER
Would you like to start/restart the LDAP-UX daemon (y/n) ? [y]: ENTER
```

Congratulations :smile: your HPUX server is fully autheticated against ldap. 

Your thoughts and suggestions are always welcome, please feel free to comment or ask questions if you need a hand. 

