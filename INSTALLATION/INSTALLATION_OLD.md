===
### Table of Contents
1. [Getting Started](#gettingstarted)

   1.1. [FonB Solution Architecture](#solution-architecture)

2. [System Requirements](#systemrequirements)

   2.1. [OS Requirements](#osrequirements)

   2.2. [Asterisk Version Support](#asteriskversion)

   2.3. [Browser Requirement](#browserrequirement)

3. [Package Dependencies](#dependencies)

   3.1. [PHP Installation](#php-installation)

   3.2. [PHP-MySQL Installation](#php-mysql-installation)

   3.3. [PHP-CGI Installation](#php-cgi-installation)

   3.4. [MySQL Server Installation](#mysql-server-installation)

   3.5. [MySQL Client Installation](#mysql-client-installation)

   3.6. [PHP-XML Installation](#php-xml-installation)
   
   3.7. [PHP Compilation (for Elastix Users)](#php-compilation)

4. [Downloading FonB](#downloadingfonb)

5. [Installation & Configuration](#configuration)

   5.1. [Decompress TAR Files](#decompress-tar)

   5.2. [Create phoneb.cfg File](#create-phonebcfg)

   5.3. [Create users.cfg file](#create-userscfg)

   5.4. [Activate License](#activate-license)

   5.5. [Permit Subnet in manager.conf](#permit-subnet)

   5.6. [Create FonB MySQL Database](#create-fonb-mysql)

   5.7. [Configure IonCube Encoder](#configure-ioncube)

   5.8. [Modify Asterisk MySQL CDR DB For Highrise Entries](#modify-mysqlcdr)

   5.9. [Modify extensions_custom.conf To Enable Active Calls](#modify-extensionscustom)

6. [Logging In](#login)

7. [FonB CLI Switches](#cli-switches)
___

<a name="gettingstarted"/>
### 1. Getting Started
FonB is web based software addon to provide additional features to your Asterisk Server. These features include Phonebook for Asterisk, Highrise CRM Integration, FonB XML App for IP Phones, Call History XML App for IP Phones, Active Call Panel, Call Switching, Number Lookup, Number Rewrites etc. This document provides instructions to download, install and configure FonB from scratch. It is assumed that you have a fully functional Asterisk Server running in environment where FonB is to be installed. For integration with Highrise, it's assumed that you have a working Highrise CRM account to be integrated with Aptus FonB. Please be noted that this document is expected to be modified with time based on user input we are going to receive. Please feel free to report any issues in support forum.
<a name="solution-architecture"/>
####1.1. FonB Solution Architecture
FonB connects with Asterisk, Google, MySQL and Highrise (based on license type). With Asterisk, FonB needs to communicate with Asterisk AMI (manager.conf), Asterisk Dialplan (extensions.conf and extensions_custom.conf), Asterisk Call Recording Files (xxxxxx) and Asterisk CDR Database.

FonB communicates with MySQL for two reasons, first to handle its own records in a database and secondly, to deal with Asterisk CDR records.

User is supposed to inform FonB about these files in 3 configuration files, i.e. phoneb.cfg, users.cfg and php.ini (within FonB Installation directory), as explained in this document, section 5.

___
<a name="systemrequirements"/>
### 2. System Requirements
FonB can be installed on any standard x86/x86_64 based hardware system. We recommend system memory to be more than 512MB specially if you are using Elastix or AsteriskNOW which requires PHP Compilation. Operating System and Browser specific requirements are as under:
<a name="osrequirements"/>
####2.1. OS Requirements
FonB has currently been tested on Ubuntu 12.04, 12.10, 13.04, CentOS 6.2, 6.3, 6.4.

The application is expected to run on other versions too however, it will be confirmed once tested.

<a name="asteriskversion"/>
####2.2. Asterisk Version Support
FonB has been tested on Asterisk 1.8, 10 and 11. It may work on other versions however it will be confirmed once tested.

<a name="browserrequirement"/>
####2.3. Browser Requirement
Mozilla Firefox 6, Mozilla Firefox 5, Google Chrome 14, Safari 5, Opera 11
___
<a name="dependencies"/>
### 3. Package Dependencies
FonB requires these packages to be installed and work properly:

* PHP
* PHP-MySQL
* PHP-CGI
* MySQLServer
* MySQLClient
* PHP-XML
* PHP Compilation (For Elastix Users)

If you are installing FonB on servers running Asterisk forks like FreePBX, Elastix or PIAF, you most probably will have PHP, PHP-MySQL, MySQLServer & MySQLClient already installed.
<a name="php-installation"/>
####3.1. PHP Installation
For CentOS based systems
`yum install php` 

For Debian based systems
`apt-get install php`
<a name="php-mysql-installation"/>
####3.2. PHP-MySQL Installation
For CentOS based systems
`yum install php-mysql` 

For Debian based systems
`apt-get install php-mysql`
<a name="php-cgi-installation"/>
####3.3. PHP-CGI Installation
For CentOS based systems
`yum install php-cgi` 

For Debian based systems
`apt-get install php-cgi`
<a name="mysql-server-installation"/>
####3.3. MySQL Server Installation
For CentOS based systems
`yum install mysql-server` 

For Debian based systems
`apt-get install mysql-server`

You can install specific version however it should comply the requirements as described in section 3.
<a name="mysql-client-installation"/>
####3.4. MySQL Client Installation.
For CentOS based systems
`yum install mysql-client` 

For Debian based systems
`apt-get install mysql-client`

You can install specific version however it should comply the requirements as described in section 3.
<a name="php-xml-installation"/>
####3.5. PHP-XML Installation.
For CentOS based systems
`yum install php-xml` 

For Debian based systems
`apt-get install php-xml`

<a name="php-compilation" />
####3.6. PHP Compilation (for Elastix Users)
Please refer to [this](#https://github.com/aptus/FonB-Documentation/blob/master/MISC/TIPS.md#phpcompilation) section to see the steps you need to take for PHP Compilation. This step is mandatory only for Elastix Users.

___
<a name="downloadingfonb"/>
###4. Downloading FonB Executables
FonB is a commercial software available for 30 days free trial in a compressed `.tar` format [here](http://www.aptus.com/download.html). After 30 days, FonB will stop working and you will need to buy a license using a credit card. Once the license is purchased, it will be emailed to you on the registered email, along with the instructions to activate it (also explained here in Section 6).

Make sure your OS and Asterisk version is specified in Section 2. If it's not, just wait while we are working to release it on many other Linux distros and Asterisk forks.
___
<a name="configuration"/>
###5. Installation & Configuration
The [download link](http://www.aptus.com/download.html) will provide you `phoneb.tar` file. Download it and place it in the directory you want to install FonB in. In our case, we will be installing PhoneB in `/usr/local` directory, so place the downloaded file here (using `wget http://aptus.com/download/fonb/phoneb_1.0.1.tgz`). From here, follow the given steps:

<a name="decompress-tar"/>
####5.1. Decompress the tar files:
`tar xvzf phoneb_1.0.1.tgz`
<a name="create-phonebcfg"/>
####5.2. Setting phoneb.cfg file
FonB executable is found in `/phoneb/bin/` directory. In order to run FonB, you need to create two files, i.e. phoneb.cfg and users.cfg. These files can be created in any of the directories `/etc/phoneb/phoneb.cfg`  or `/usr/local/phoneb/etc/phoneb.cfg`
Please note that phoneb.cfg and users.cfg should be in a same directory. For the sake of example, we will be creating these two files in `/etc/phoneb/`. you need to create this directory by `mkdir /etc/phoneb/`
#####5.2.1. Sample phoneb.cfg
Here is the sample phoneb.cfg file. You can create yours using `vim /etc/phoneb/phoneb.cfg` and then by pressing `a` and then pasting the sample text with necessary chages. Once done, press `Esc` and then `:wq` to save the file and hit `Enter` and exit:

```
[PhoneB]
BaseDir= /usr/local/PhoneB            ;Directory Location for FonB
UsersConfFile= /etc/phoneb/users.cfg  ;Directory Location for FonB Configuration files (phoneb.cfg &
                                      ;users.cfg)
ListenPort= 8080                      ;Listen Port for Web Server. If you are using FreePBX or Elastix
                                      ;use 8080
AsteriskMonitorPath= /var/spool/asterisk/monitor             ;Path for Recording Files Folder
LameExec= /usr/local/PhoneB/bin/lame                         ;Path for wav to mp3 converter for Browser
                                                             ;Presentation
;PhpCgiPath = "/usr/bin/php-cgi"                             ;Custom path for PHP-CGI Folder (use with Elastix, needs PHP Compilation)



[AMI]                                 ;Delete this section if you don't want to use Active Calls feature
ManagerHost= localhost                ;Hostname/IP of your Asterisk Server
ManagerPort= 5038                     ;AMI Port
ManagerUsername= someusername         ;Username as per /etc/asterisk/manager.conf      
ManagerPassword= somepassword         ;Password as per /etc/asterisk/manager.conf (Special Characters not supported)

[MysqlCdr]
Table= cdr
Username= someusername                ;Username as per /etc/asterisk/cdr_mysql.conf
Password= somepassword                ;Password as per /etc/asterisk/cdr_mysql.conf (Special Characters not supported)
Database= asteriskcdrdb               ;Database name as per /etc/asterisk/cdr_mysql.conf
Hostname= localhost                   ;Hotname/IP of your MySQL Database Server

[MysqlFonB]
Username= someusername                ;Username
Password= somepassword                ;Password  (Special Characters not supported)
Database= phonebook                   ;Database Name
Hostname= localhost                   ;Hotname/IP of your MySQL Database Server

```
<a name="create-userscfg"/>
####5.3. Create users.cfg File
The configuration file `users.cfg` enables Asterisk users to use Aptus FonB. All the user extensions specified here must be the part of Asterisk extensions.conf file. You can create your file by using command `vim /etc/phoneb/users.cfg`, press `a` and then by pasting the given sample file with necessary changes. Once done, press `Escape` button followed by `:wq` and then hit `Enter`.

#####5.3.1. Sample users.cfg 
Here is the sample users.cfg file:

```
[3000]                                 
Extension=3000                         ;Extension
Terminal=SIP/3000                      ;Extension type as defined in Extensions.conf
Context=from-internal                  ;Extension context
Name=max                               ;Name of the user
Password=3333                          ;Password for Weblogin (Special Characters not supported, Numerics Recommended, 	easier to use in IP Phones)
Language=en                            ;Language
DID=4547733                            ;Internal DID for Asterisk CLID Lookup 
Department=Development                 ;Department Name
Spy=all                                ;Listen to all Other Calls
Company=aptus                          ;Company Name, important for Multi Tenant Environment
;Type=deleted                          ;Will be used when extension is deleted and you want to keep the data without login
Queue=601,602,603		       ;Queue Groups this extension is part of
RingGroups=501,502,503		       ;Ring Groups this extension is part of


[3001]
Extension=3001                         ;Extension
Terminal=SIP/3001                      ;Extension type as defined in Extensions.conf
Context=from-internal                  ;Extension context
Name=sam                               ;Name of the user
Password=3333                          ;Password for Weblogin (Special Characters not supported, Numerics Recommended, easier to use in IP Phones)
Language=en                            ;Language
DID=4547744                            ;Internal DID for Asterisk CLID Lookup 
Department=Development                 ;Department Name
Spy=all                                ;Listen to all Other Calls
Company=aptus                          ;Company Name, important for Multi Tenant Environment
;Type=deleted                          ;Will be used when extension is deleted and you want to keep the data without login
Queue=601			       ;Queue Groups this extension is part of
RingGroups=501			       ;Ring Groups this extension is part of

[601]				       ;Queue Group as defined in FreePBX/Asterisk
Name = FirstQueue		       ;Name of the Queue Group
Extension = 601			       ;Extension you need to dial to call this Queue Group
Type = queue			       ;Type of this context
Department = Sales		       ;Department this Queue Group belongs to

[600]
Name = Tech Room		       ;Name of this Ring Group
Extension = 600			       ;Extension you need to dial to call this ring group
Department = Technical		       ;Department this Ring Group is part of
Type = ringgroup		       ;Type of this context

                                       
                                       
```
<a name="activate-license"/>
####5.4. Activate License
You need to run `/usr/local/PhoneB/bin/phoneb` executable once phoneb.cfg and users.cfg are configured properly. If everything is good, you will be prompted for Activation License. If you have purchased FonB, you must enter the license key you have been provided on your registered email. In case of free 30 days demo, you just need to press enter. You must have an Internet Connection at this stage. If in case you come across any error like `-bash: ./phoneb: /lib/ld-linux.so.2: bad ELF interpreter: No such file or directory`, install `glibc` libraries. See [this link](https://github.com/aptus/FonB-Documentation/blob/master/INSTALLATION/TIPS.md#x86_64-glibc-error) for further details.

p.s: You might have to repeat this step for 2-3 times. This is a minor bug that will be fixed soon.

```
[root@localhost phoneb]# /usr/local/PhoneB/bin/phoneb 
2013/07/12 10:13:39 phoneb.cfg located @  /etc/phoneb/phoneb.cfg
2013/07/12 10:13:39 PhoneB BaseDir: /usr/local/PhoneB
2013/07/12 10:13:39 users.cfg located @  /etc/phoneb/users.cfg
2013/07/12 10:13:39 License not found or not complete
Please entre your activation key OR entre nothing to request a 30 day demo license. (Internet is required)
Activation Key:  
Demo_License_FonB_V1 license was retrieved from the activation server. This license will be deployed on your next FonB restart.
[root@localhost phoneb]# 
```
<a name="permit-subnet"/>
####5.5. Permit Subnet in Manager.conf
In your `/etc/asterisk/manager.conf` search for deny/permit setting for different IPs/Subnets and change it to permit your subnet for connectivity. This step is mandatory as of now, even if you are running Asterisk and FonB on same machine.

```
deny=0.0.0.0/0.0.0.0
permit=10.0.8.0/255.255.255.255
permit=127.0.0.1/255.255.255.0

```
Also, under your manager.conf user (admin), make sure following permissions are there for read and write. You can also replace them with 'all':

```
read = system,call,log,verbose,command,agent,user,config,command,dtmf,reporting,cdr,dialplan,originate
write = system,call,log,verbose,command,agent,user,config,command,dtmf,reporting,cdr,dialplan,originate
```
<a name="create-fonb-mysql"/>
####5.6. Create FonB MySQL Database
FonB uses MySQL Database to store all of its information. At this stage, you need to create a database to be used by FonB. Please note that Special Characters are not supported in current release of FonB (due to certain restrictions in PHP Configuration Syntax).

```
mysql -u root -p
Enter password: yourmysqlpassword
mysql> CREATE DATABASE phonebook;
mysql> CREATE USER 'username'@'localhost' IDENTIFIED BY 'userpassword';
mysql> GRANT ALL PRIVILEGES ON *.* TO 'username'@'localhost' WITH GRANT OPTION; 
```

<a name="configure-ioncube"/>
####5.7. Configure Ioncube Encoder
In order to display the PHP pages, you need to configure Ioncure Encoder bundled with FonB. Prior to that, you need to confirm if your system is 32 bit (x86) or 64 bit (x86_64). Use `uname -m` to check this.

For 64bit system it will be like:

```
max@max-ThinkPad-T430:/$ uname -m
x86_64
```

For 32bit system, it will be like:
```
[root@localhost ~]# uname -m
i686
```

Secondly, check out the PHP version you are running, using `php -v`:
```
[root@localhost ~]# php -v
PHP 5.3.3 (cli) (built: May  7 2012 19:58:17) 
Copyright (c) 1997-2010 The PHP Group
Zend Engine v2.3.0, Copyright (c) 1998-2010 Zend Technologies
    with Zend Guard Loader v3.3, Copyright (c) 1998-2010, by Zend Technologies
```
You need to commit a couple of changes now based on your System Architecture (32 or 64 bit) and the PHP version you are running (5.3.3 in this case). Edit `/usr/local/PhoneB/php/php.ini`:

```
date.timezone = "Asia/Dubai"                ;Your time zone, important to correctly display Call History
magic_quotes_gpc = Off
magic_quotes_runtime = Off
max_execution_time = 0
max_input_time = 180
register_argc_argv = On
file_uploads = On
upload_tmp_dir = /tmp
upload_max_filesize = 55M
post_max_size = 55M
html_errors = Off
include_path = ".:/etc/inc:./classes:./include"
log_errors = On
error_log = "/tmp/php4.log"
display_startup_errors = Off
auto_detect_line_endings = On
; extension=mysql.so
session.save_path=/tmp/sessions
zend_extension = ./ioncube32/ioncube_loader_lin_5.3.so  ;this needs to be changed as per your system
```

The parameter `zend_extension = ./ioncube32/ioncube_loader_lin_5.3.so` should be changed as per your system architecture and PHP version. For 32 bit systems running PHP 5.3, it will remain as it is. For 64 bit systems running PHP 5.3 it will become: 
`zend_extension = ./ioncube64/ioncube_loader_lin_5.3.so`

For 64 bit system running PHP 5.1 it will be:

`zend_extension = ./ioncube64/ioncube_loader_lin_5.1.so`

And so on for any other version of PHP.

<a name="modify-mysqlcdr"/>
####5.8. Modify Asterisk MySQL CDR DB For Highrise Entries
In order to save Highrise related data to your Asterisk CDR, you will be required to modify your MySQL CDR Database.

```
mysql -u root -p
Enter password: yourmysqlpassword
mysql> use asteriskcdrdb                                          ;or the DB your Asterisk is using
mysql> ALTER TABLE cdr ADD FonBCallUniqueID VARCHAR(80) NOT NULL;
Query OK, 3040 rows affected (0.07 sec)
Records: 3040  Duplicates: 0  Warnings: 0

mysql> ALTER TABLE cdr ADD FonBCallNotes VARCHAR(80) NOT NULL;
Query OK, 3040 rows affected (0.08 sec)
Records: 3040  Duplicates: 0  Warnings: 0

mysql> ALTER TABLE cdr ADD FonBHighriseNoteID VARCHAR(80) NOT NULL;
Query OK, 3040 rows affected (0.08 sec)
Records: 3040  Duplicates: 0  Warnings: 0

```
<a name="modify-extensionscustom"/>
####5.9. Modify extensions_custom.conf To Enable Active Calls
In your Asterisk Config folder `/etc/asterisk`, edit the file `extensions_custom.conf` and append it with following:

```
[OnHold]
exten => s,1,Answer()
;exten => s,2,MusicOnHold()
exten => s,2,Hangup

exten => hold,1,Answer()
exten => hold,n,MusicOnHold(,3600)
exten => hold,n,Hangup

exten => wait,1,NoCDR()
;exten => wait,n,StopMixMonitor()
exten => wait,n,Answer()
exten => wait,n,UserEvent(FonBCallSwitch,Channel: ${CHANNEL(name)})
exten => wait,n,Wait(50)
exten => wait,n,Hangup

[Conference]
exten =>  Conference,1,MeetMe(${MEETME_ROOMNUM},dqMx)

;Conference test extension: admin
exten =>  admin,1,MeetMe(${MEETME_ROOMNUM},qdMxp)
exten =>  admin,n,MeetMeAdmin(${MEETME_ROOMNUM},K)

[Hangup]
exten => Hangup,1,NoCDR()
exten => Hangup,n,Hangup()
```
Reload your Asterisk once done.

<a name="login"/>
###6. Logging In
Now that everything has been done, you can safely login on hostname:[port] (localhost:8080 or 10.0.8.200:8080 in our case). Login using credentials you specified in `users.cfg`.
View [Tips Page](https://github.com/aptus/FonB-Documentation/blob/master/MISC/TIPS.md) to rectify any possible error while using FonB.


<a name="cli-switches"/>
###7. FonB CLI Switches
Starting from FonB v1.0.2, FonB Command Line Interface Switches are used to use FonB Command with certain predefined options. These switches are explained as under:


Usage:

      phoneb [Options] <value>

Valid Options:

    --help  Print a brief help message and exits

    --version
            Display the version and exits

    --activate XXXX-XXXX-XXXX-XXXX
            License this server using the provided activation-key 
            (this operation requires an Internet connection with
            our activation server)

    --force-activate XXXX-XXXX-XXXX-XXXX
            Continue with the FailedRevoke activation of your key.
	    This will lock your activation key to work only with 
	    this server. Do revoke your key next time before 
	    formatting. Otherwise, the frequent use of this option,
	    will blacklist your activation-key.

    --revoke
            Revoke the current license to re-activate the used
            activation-key with another server (this operation
            requires an Internet connection with our activation
            server)

    --print-mac-address
            Print the first MAC address used on your server
``
