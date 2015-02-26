FonB Installation Guide for Thirdlane Users
==================
===
### Table of Contents
1. [Getting Started](#gettingstarted)

  1.1. [FonB Solution Architecture](#solution-architecture)
  
  1.2. [FonB RPM For Thirdlane](#thirdlane-rpm)

2. [System Requirements](#systemrequirements)

  2.1. [OS Requirements](#osrequirements)

  2.2. [Browser Requirement](#browserrequirement)
   
3. [Installation Script - An Overview](#rpm-installation)

  3.1. [Requirements](#download)
  
  3.2. [Install FonB RPM](#install-rpm)
  
  3.3. [Get Your FonB License Key](#get-license)
  
  3.4. [Create Thirdlane Users](#create-thirdlane-users)
  
  3.5. [Accessing FonB](#access-fonb)
  
  3.6. [Open FonB Tab In Thirdlane](#open-fonb-tab)
  
  3.7. [Activate Your License Key](#activate-license)
  
  3.8. [Define FonB Users](#define-fonb-users)
  
  3.9. [Login](#login)
  
___
<a name="gettingstarted"/>
### 1. Getting Started
FonB is web based software addon to provide additional features to your Asterisk Server. These features include Phonebook for Asterisk, Highrise CRM Integration, FonB XML App for IP Phones, Call History XML App for IP Phones, Active Call Panel, Call Switching, Number Lookup, Number Rewrites etc. This document provides instructions to download, install and configure FonB from scratch. It is assumed that you have a fully functional Asterisk Server running in environment where FonB is to be installed. For integration with Highrise, it's assumed that you have a working Highrise CRM account to be integrated with Aptus FonB. Please be noted that this document is expected to be modified with time based on user input we are going to receive. Please feel free to report any issues in support forum.
<a name="solution-architecture"/>
####1.1. FonB Solution Architecture
FonB connects with Asterisk, Google, MySQL and Highrise (based on license type). With Asterisk, FonB needs to communicate with Asterisk AMI (manager.conf), Asterisk Dialplan (extensions_base.include and extensions.conf), Asterisk Call Recording Files (xxxxxx) and Asterisk CDR Database.

FonB communicates with MySQL for two reasons, first to handle its own records in a database and secondly, to deal with Asterisk CDR records.

<a name="thirdlane-rpm"/>
####1.2. FonB RPM for Thirdlane 
The newest FonB RPM for Thirdlane will further stabilize the application as a whole as well as provide GUI running on a separate port independent of Thirdlane.

___
<a name="systemrequirements"/>
### 2. System Requirements
FonB can be installed on any standard x86_64 based hardware system. We recommend system memory to be more than 512MB.
<a name="osrequirements"/>
####2.1. OS Requirements
In order to install FonB RPM for Thirdlane, user must have at least Thirdlane Single Tenant or Multi Tenants.

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
wget http://repo.aptus.com/thirdlane/x86_64/aptus_64.repo
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

<a name="create-Thirdlane-users"/>
#### 3.4. Create Thirdlane Users
Go to `Extensions and Contacts>User Extensions` in Thirdlane Web UI and create new users with extension associated with them. Any user with type Admin will be able to see FonB Admin Portal with Backend Settings whereas anyone with type Operator or Extension will see only User Panel.

<a name="access-fonb"/>
#### 3.5 Accessing FonB
After you have successfully installed FonB and obtained the license from (www.aptus.com/download.html), it's time to get started with FonB, please note that FonB runs on port (8889), therefore, to access FonB, simply open your browser and type the IP address where FonB is installed followed by :8889 like
````
<IP_ADDRESS>:8889
````
You will be redirected to license page, after you insert your license and activate FonB, you will see in the right corner (Admin Portal), clicking that link will redirect you to Admin portal, the password for the Admin portal can be obtained by typing this command in your server's CLI:
````
grep AdminPassword /etc/phoneb/phoneb.cfg
````
Copy paste the password, and you're ready to go.

<a name="activate-license"/>
#### 3.6. Activate Your License
Login with Admin credentials to FonB Admin Portal. Navigate to Licenses and Apply the Activation Key by entering the key followed by Activation Button.

<a name="define-fonb-users"/>
#### 3.7. Define Users In FonB
Go to Users Tab in Admin Portal and define your users. If you already have users in Thirdlane, you just need to add Password for them as well as set the type of the user (i.e. Web, Mobile, Web + Mobile).

<a name="login"/>
#### 3.8. Login
You can now login with any defined user in FonB with extension associated in `Thirdlane>Extensions and Contacts>User Extensions`. Make sure you set extension password in the Admin portal in order to be able to login to FonB.


