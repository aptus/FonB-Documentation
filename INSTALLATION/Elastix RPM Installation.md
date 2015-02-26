FonB Installation Guide for Elastix Users
==================
===
### Table of Contents
1. [Getting Started](#gettingstarted)

  1.1. [FonB Solution Architecture](#solution-architecture)
  
  1.2. [FonB Python Script (Deprecated)](#install-script)
  
  1.3. [FonB RPM For Elastix](#elastix-rpm)

2. [System Requirements](#systemrequirements)

  2.1. [OS Requirements](#osrequirements)

  2.2. [Browser Requirement](#browserrequirement)
   
3. [Installation Script - An Overview](#rpm-installation)

  3.1. [Requirements](#download)
  
  3.2. [Install FonB RPM](#install-rpm)
  
  3.3. [Get Your FonB License Key](#get-license)
  
  3.4. [Create Elastix Users](#create-elastix-users)
  
  3.5. [Open FonB Tab In Elastix](#open-fonb-tab)
  
  3.6. [Accessing FonB out of Elastix tab](#access-fonb)
   
  3.7. [Activate Your License Key](#activate-license)
  
  3.8. [Define FonB Users](#define-fonb-users)
  
  3.9. [Login](#login)
  
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
Previous builds of FonB Install Script were all Python based script that could install FonB on Elastix Systems. There was however a part where user had to logon to backend system and define/alter couple of files in CLI mode. Besides, any modification in FonB Administration required CLI access of the system which was confusing and sometimes not desirable by many users.

<a name="elastix-rpm"/>
####1.3. FonB RPM for Elastix
The newest FonB RPM for Elastix will further stabilize the application as a whole as well as provide GUI running on a separate port independent of Elastix.


___
<a name="systemrequirements"/>
### 2. System Requirements
FonB can be installed on any standard x86/x86_64 based hardware system. We recommend system memory to be more than 512MB.
<a name="osrequirements"/>
####2.1. OS Requirements
In order to install FonB RPM for Elastix, user must have at least Elastix 2.4 or higher.

####2.2. Browser Requirement
Mozilla Firefox 6, Mozilla Firefox 5, Google Chrome 14, Safari 5, Opera 11
___

<a name="rpm-installation"/>
### 3. RPM Installation Procedure


<a name="download"/>
#### 3.1. Download FonB In Your YUM Repo
Go to directory /etc/yum.repos.d using cd /etc/yum.repos.d and download FonB. For x64 systems use:
```
cd /etc/yum.repos.d/
wget http://repo.aptus.com/elastix/x86_64/aptus_64.repo
```
For x32 systems, use:
```
cd /etc/yum.repos.d/
wget http://repo.aptus.com/elastix/i386/aptus.repo
```


<a name="install-rpm"/>
#### 3.2. Install FonB RPM
Install FonB using:

```
yum install fonb-elastix
```

<a name="get-license"/>
#### 3.3. Get License Key
Go to Aptus Website (www.aptus.com/download.html) and register yourself to get a 30 days demo activation key. Alternatively, you can purchase licenses from Pricing Page (http://aptus.com/prices.html)

<a name="create-elastix-users"/>
#### 3.4. Create Elastix Users
Go to `System>Users` in Elastix Web UI and create new users with extension associated with them. Any user with type Admin will be able to see FonB Admin Portal with Backend Settings whereas anyone with type Operator or Extension will see only User Panel.

<a name="open-fonb-tab"/>
#### 3.5. Open FonB Tab In Elastix
For Elastix Admins, FonB tab is under the Extras (right most drop down menu) within Elastix. For regular users, it’s visible on Elastix Top Menu. Once you click FonB, you will be taken straight into your FonB User Portal without any login. This happens when you’ve associated your Extension with logged in user. You will see a login page only if you have no extensions in FonB OR there’s no extension associated with Logged in user.

<a name="access-fonb"/>
#### 3.6. Accessing FonB out of Elastix tab
To access FonB outside of Elastix tab, open your broswer and simply type your IP address followed by (:8889) like
````
<IP_ADDRESS>:8889
````
This will take you straight to user login page, if you wish to go to Admin login, simply click the link on the top right of your broswer page (Admin Portal).
Please note that you will need Admin password to access the Admin page, you can find your password by typing the following command in your Elastix's CLI:
````
grep AdminPassword /etc/phoneb/phoneb.cfg
````
Copy & paste the password, and you're ready to go.

<a name="activate-license"/>
#### 3.7. Activate Your License
Login with Admin credentials to your Elastix Web UI. Navigate to FonB tab > Licenses and Apply the Activation Key by entering the key followed by Activation Button.

<a name="define-fonb-users"/>
#### 3.8. Define Users In FonB
Go to Users Tab in Admin Portal and define your users. If you already have users in Elastix, you just need to add Password for them as well as set the type of the user (i.e. Web, Mobile, Web + Mobile).

<a name="login"/>
#### 3.9. Login
You can now login with any defined user in FonB with extension associated in `Elastix>System>Users`.


