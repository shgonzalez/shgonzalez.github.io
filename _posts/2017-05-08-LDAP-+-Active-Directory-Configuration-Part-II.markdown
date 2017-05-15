---
layout: post
categories: Linux LDAP ActiveDirectory
published: false
---

In this section we are going to cover:
* 389-Directory Server Setup
* Configure TLS for secure connections with self signed certificate
* Configure unauthenticated binds
* LDAP Client configuration


Now you have a fully authenticated Linux servers against ActiveDirectory, now it's to install 389 Directory Server

389-Directory Server Setup


Now install the EPEL report

yum install -y epel-release.noarch
 
Now install the following packages

yum install 389-ds-base 389-admin 389-console 389-ds-console 389-admin-console -y


After that configure the file descriptor parameter

vim /etc/sysconfig/dirsrv.systemd
[Service]
LimitNOFILE=8192

Now, run the /usr/sbin/setup-ds-admin.pl

==============================================================================
This program will set up the 389 Directory and Administration Servers.

It is recommended that you have "root" privilege to set up the software.
Tips for using this program:
  - Press "Enter" to choose the default and go to the next screen
  - Type "Control-B" then "Enter" to go back to the previous screen
  - Type "Control-C" to cancel the setup program

Would you like to continue with set up? [yes]:

==============================================================================
Your system has been scanned for potential problems, missing patches,
etc.  The following output is a report of the items found that need to
be addressed before running this software in a production
environment.

389 Directory Server system tuning analysis version 14-JULY-2016.

NOTICE : System is x86_64-unknown-linux3.10.0-123.el7.x86_64 (1 processor).

NOTICE : The net.ipv4.tcp_keepalive_time is set to 7200000 milliseconds
(120 minutes).  This may cause temporary server congestion from lost
client connections.

WARNING: There are only 1024 file descriptors (soft limit) available, which
limit the number of simultaneous connections.

WARNING  : The warning messages above should be reviewed before proceeding.

Would you like to continue? [no]: **yes**

==============================================================================
Choose a setup type:

   1. Express
       Allows you to quickly set up the servers using the most
       common options and pre-defined defaults. Useful for quick
       evaluation of the products.

   2. Typical
       Allows you to specify common defaults and options.

   3. Custom
       Allows you to specify more advanced options. This is
       recommended for experienced server administrators only.

To accept the default shown in brackets, press the Enter key.

Choose a setup type [2]: **ENTER**

==============================================================================
Enter the fully qualified domain name of the computer
on which you're setting up server software. Using the form
<hostname>.<domainname>
Example: eros.example.com.

To accept the default shown in brackets, press the Enter key.

Warning: This step may take a few minutes if your DNS servers
can not be reached or if DNS is not configured correctly.  If
you would rather not wait, hit Ctrl-C and run this program again
with the following command line option to specify the hostname:

    General.FullMachineName=your.hostname.domain.name

Computer name [ds1.sergio.lab]: **ENTER**

==============================================================================
The servers must run as a specific user in a specific group.
It is strongly recommended that this user should have no privileges
on the computer (i.e. a non-root user).  The setup procedure
will give this user/group some permissions in specific paths/files
to perform server-specific operations.

If you have not yet created a user and group for the servers,
create this user and group using your native operating
system utilities.

System User [dirsrv]: **ENTER**
System Group [dirsrv]: **ENTER**

==============================================================================
Server information is stored in the configuration directory server.
This information is used by the console and administration server to
configure and manage your servers.  If you have already set up a
configuration directory server, you should register any servers you
set up or create with the configuration server.  To do so, the
following information about the configuration server is required: the
fully qualified host name of the form
<hostname>.<domainname>(e.g. hostname.example.com), the port number
(default 389), the suffix, the DN and password of a user having
permission to write the configuration information, usually the
configuration directory administrator, and if you are using security
(TLS/SSL).  If you are using TLS/SSL, specify the TLS/SSL (LDAPS) port
number (default 636) instead of the regular LDAP port number, and
provide the CA certificate (in PEM/ASCII format).

If you do not yet have a configuration directory server, enter 'No' to
be prompted to set up one.

Do you want to register this software with an existing
configuration directory server? [no]: **ENTER**

==============================================================================
Please enter the administrator ID for the configuration directory
server.  This is the ID typically used to log in to the console.  You
will also be prompted for the password.

Configuration directory server
administrator ID [admin]: **ENTER**
Password:
Password (confirm):

==============================================================================
The information stored in the configuration directory server can be
separated into different Administration Domains.  If you are managing
multiple software releases at the same time, or managing information
about multiple domains, you may use the Administration Domain to keep
them separate.

If you are not using administrative domains, press Enter to select the
default.  Otherwise, enter some descriptive, unique name for the
administration domain, such as the name of the organization
responsible for managing the domain.

Administration Domain [sergio.lab]: **ENTER**

==============================================================================
The standard directory server network port number is 389.  However, if
you are not logged as the superuser, or port 389 is in use, the
default value will be a random unused port number greater than 1024.
If you want to use port 389, make sure that you are logged in as the
superuser, that port 389 is not in use.

Directory server network port [389]: **ENTER**

==============================================================================
Each instance of a directory server requires a unique identifier.
This identifier is used to name the various
instance specific files and directories in the file system,
as well as for other uses as a server instance identifier.

Directory server identifier [ds1]: **ENTER**

==============================================================================
The suffix is the root of your directory tree.  The suffix must be a valid DN.
It is recommended that you use the dc=domaincomponent suffix convention.
For example, if your domain is example.com,
you should use dc=example,dc=com for your suffix.
Setup will create this initial suffix for you,
but you may have more than one suffix.
Use the directory server utilities to create additional suffixes.

Suffix [dc=sergio, dc=lab]: **ENTER**

==============================================================================
Certain directory server operations require an administrative user.
This user is referred to as the Directory Manager and typically has a
bind Distinguished Name (DN) of cn=Directory Manager.
You will also be prompted for the password for this user.  The password must
be at least 8 characters long, and contain no spaces.
Press Control-B or type the word "back", then Enter to back up and start over.

Directory Manager DN [cn=Directory Manager]: **ENTER**
Password:
Password (confirm):

==============================================================================
The Administration Server is separate from any of your web or application
servers since it listens to a different port and access to it is
restricted.

Pick a port number between 1024 and 65535 to run your Administration
Server on. You should NOT use a port number which you plan to
run a web or application server on, rather, select a number which you
will remember and which will not be used for anything else.

Administration port [9830]: **ENTER**

==============================================================================
The interactive phase is complete.  The script will now set up your
servers.  Enter No or go Back if you want to change something.

Are you ready to set up your servers? [yes]: **ENTER**
Creating directory server . . .
Your new DS instance 'ds1' was successfully created.
Creating the configuration directory server . . .
Beginning Admin Server creation . . .
Creating Admin Server files and directories . . .
Updating adm.conf . . .
Updating admpw . . .
Registering admin server with the configuration directory server . . .
Updating adm.conf with information from configuration directory server . . .
Updating the configuration for the httpd engine . . .
Starting admin server . . .
The admin server was successfully started.
Admin server was successfully created, configured, and started.
Exiting . . .
Log file is '/tmp/setupy75GD8.log'



Ensure your services will run after reboot

systemctl enable dirsrv.target dirsrv@ds1.service dirsrv-admin.service
systemctl start dirsrv.target dirsrv@ds1.service dirsrv-admin.service



Configuring Certificates

Our 389 Directory Server is up and running, now we must configure certificates so our Linux clients can LogIn against Directory Server.
In this approach we don't use a Certificate Authority instead we'll act as a Certificaty Authority (CA) signing our certificate in the same server.

 cd /etc/dirsrv/slapd-ds1/
 vim pwdfile.txt
 aStrongPasswordHere

 Create a new certificate database
 
 certutil -N -d . -f pwdfile.txt
 
 Create de CA Certificate
 
 certutil -S -n "CA certificate" -s "cn=CA Server,dc=sergio,dc=lab" -x -t "CT,," -m 1000 -v 120 -d . -f pwdfile.txt -2
 Generating key. This may take a few moments...
Is this a CA certificate [y/N]? y
Enter the path length constraint, enter to skip [<0 for unlimited path]: >
Is this a critical extension [y/N]? y
 
 Now we create the Server Cert which the issuer CA is the previous created
 
 certutil -S -n "Server-Cert" -s "cn=ds.sergio.lab" -c "CA certificate" -t "u,u,u" -m 1001 -v 120 -d . -k rsa -f pwdfile.txt
 
 Now export the CA certificate, which clients will verify to create a secure connection TLS/SSL. This is typically copied to /etc/openldap/cacerts/
 
 certutil -d . -L -n "CA certificate" -a > cacert.pem
 
 
Now it's time to set correct permissions

chown dirsrv:dirsrv key3.db cert8.db pwdfile.txt
chmod 640 key3.db cert8.db pwdfile.txt

