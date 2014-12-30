FonB Installation Guide for FreePBX Users
==================
===
### Table of Contents
1. [Getting Started](#gettingstarted)

  1.1. [FonB Solution Architecture](#solution-architecture)
  
  1.2. [FonB Python Script (Deprecated)](#install-script)
  
  1.3. [FonB RPM For FreePBX](#FreePBX-rpm)

2. [System Requirements](#systemrequirements)

  2.1. [OS Requirements](#osrequirements)

  2.2. [Browser Requirement](#browserrequirement)
   
3. [Installation Script - An Overview](#rpm-installation)

  3.1. [Requirements](#download)
  
  3.2. [Install FonB RPM](#install-rpm)
  
  3.3. [Navigate To Freshly Baked FonB](#navigate-to-fonb)
  
  3.4. [Create FreePBX Users](#create-FreePBX-users)
    
  3.5. [Activate Your License Key](#activate-license)
  
  3.6. [Get Your Unique Admin Password](#get-admin-pass)
  
  3.7. [Login To Admin Portal](#login-admin)

  3.8. [Define FonB Users](#define-fonb-users)
  
  3.9. [Login With User](#login-user)
___
<a name="gettingstarted"/>
### 1. Getting Started
FonB is web based software addon to provide additional features to your Asterisk Server. These features include Phonebook for Asterisk, Highrise CRM Integration, FonB XML App for IP Phones, Call History XML App for IP Phones, Active Call Panel, Call Switching, Number Lookup, Number Rewrites etc. This document provides instructions to download, install and configure FonB from scratch. It is assumed that you have a fully functional Asterisk Server running in environment where FonB is to be installed. For integration with Highrise, it's assumed that you have a working Highrise CRM account to be integrated with Aptus FonB. Please be noted that this document is expected to be modified with time based on user input we are going to receive. Please feel free to report any issues in support forum.
<a name="solution-architecture"/>
####1.1. FonB Solution Architecture
FonB connects with Asterisk, Google, MySQL and Highrise (based on license type). With Asterisk, FonB needs to communicate with Asterisk AMI (manager.conf), Asterisk Dialplan (extensions.conf and extensions_custom.conf), Asterisk Call Recording Files (xxxxxx) and Asterisk CDR Database.

FonB communicates with MySQL for two reasons, first to handle its own records in a database and secondly, to deal with Asterisk CDR records.

User is supposed to inform FonB about these files in 3 configuration files, i.e. phoneb.cfg, users.cfg and php.ini (within FonB Installation directory), as explained in this document, section 5.

<a name="install-script"/>
####1.2. FonB Python Script (Deprecated)
Previous builds of FonB Install Script were all Python based script that could install FonB on FreePBX Systems. There was however a part where user had to logon to backend system and define/alter couple of files in CLI mode. Besides, any modification in FonB Administration required CLI access of the system which was confusing and sometimes not desirable by many users.

<a name="FreePBX-rpm"/>
####1.3. FonB RPM for FreePBX
The newest FonB RPM for FreePBX will further stabilize the application as a whole as well as provide GUI running on a separate port independent of FreePBX.


___
<a name="systemrequirements"/>
### 2. System Requirements
FonB can be installed on any standard x86/x86_64 based hardware system. We recommend system memory to be more than 512MB.
<a name="osrequirements"/>
####2.1. OS Requirements
In order to install FonB RPM for FreePBX, user must have at least FreePBX 2.4 or higher.

####2.2. Browser Requirement
Mozilla Firefox 6, Mozilla Firefox 5, Google Chrome 14, Safari 5, Opera 11
___

<a name="rpm-installation"/>
### 3. RPM Installation Procedure


<a name="download"/>
#### 3.1. Download FonB In Your YUM Repo
Go to directory /etc/yum/repos.d using cd /etc/yum/repos.d and download FonB. For x64 systems use:
```
wget http://repo.aptus.com/freePBX/x86_64/aptus_64.repo
```
For x32 systems, use:
```
wget http://repo.aptus.com/freePBX/i386/aptus.repo
```

<a name="install-rpm"/>
#### 3.2. Install FonB RPM
Install FonB using:

```
yum install fonb*
```

<a name="get-license"/>
#### 3.3. Get License Key
Go to Aptus Website (www.aptus.com/download.html) and register yourself to get a 30 days demo activation key. Alternatively, you can purchase licenses from Pricing Page (http://aptus.com/prices.html)

<a name="navigate-to-fonb"/>
#### 3.4. Navigate to Freshly Baked FonB
Go to FonB Web URL on <IP>:8889/login. Thus, if the IP of the server where you’re installing FonB is 10.0.8.10, you will have to navigate to 10.0.8.10:8889/login.


<a name="activate-license"/>
#### 3.5. Activate Your License
Login with Admin credentials to your FreePBX Web UI. Navigate to FonB tab > Licenses and Apply the Activation Key by entering the key followed by Activation Button.

<a name="get-admin-pass"/>
#### 3.6. Get Your Unique Admin Password
Go to /etc/phoneb/phoneb.cfg and look Admin Password Parameter. The random number mentioned next to it will be your unique password. 

<a name="login-admin"/>
#### 3.7. Login To Admin Portal
Use username admin and password as the random string generated in previous step to login here.

<a name="define-fonb-users"/>
#### 3.8. Define FonB Users
Go to Users Tab in Admin Portal and define your users. If you already have users in FreePBX, you just need to add Password for them as well as set the type of the user (i.e. Web, Mobile, Web + Mobile).

<a name="login-user"/>
#### 3.9. Login With User
Now navigate back to <IP>:8889/login and use your credentials you created in last step to login. Start using FonB !




