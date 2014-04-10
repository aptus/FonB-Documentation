===
### Table of Contents
1. [Getting Started](#gettingstarted)

  1.1. [FonB Solution Architecture](#solution-architecture)

2. [System Requirements](#systemrequirements)

  2.1. [OS Requirements](#osrequirements)

  2.2. [Asterisk Version Support](#asteriskversion)

  2.3. [Browser Requirement](#browserrequirement)
   
3. [Installation Script - An Overview](#installation-script)

  3.1. [Requirements](#requirements)
   
4. [Installation Procedure](#installation-procedure)

  4.1. [Script Prompts](#script-prompts)

  4.2. [Options/Switches](#options-switches)
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
FonB has currently been tested on Ubuntu 12.04, 12.10, 13.04, CentOS 6.2, 6.3, 6.4. The application is expected to run on other versions too however, it will be confirmed once tested.

<a name="asteriskversion"/>
####2.2. Asterisk Version Support
FonB has been tested on Asterisk 1.6, 1.8, 10 and 11. It may work on other versions however it will be confirmed once tested.

<a name="browserrequirement"/>
####2.3. Browser Requirement
Mozilla Firefox 6, Mozilla Firefox 5, Google Chrome 14, Safari 5, Opera 11
___

<a name="installation-script"/>
### 3. Installation Script - An Overview
Starting from v1.1.0, FonB is now supported by an installation script to automate most of the steps involved in FonB Installation. While the documentation for manual install remains the same, this guide will show how to use the installation script.

The operation of the script is fairly easy, however, please remember that you should have your MySQL credentials with you, as these are the only login name password pairs that you need to provide manually. Rest (other then few prompts) Installation Script handles by its own.

<a name="requirements"/>
#### 3.1. Requirements

* Python 2.x (Tested on Python 2.4, 2.5, 2.6 and 2.7)
* CentOS (Freepbx/Elastix/Stock Asterisks/PBX In A Flash) (Recommended)
* Should run on any linux installation with yum. Installation will go smooth in other linux distroes too, but you will have to resolve dependencies on your own)
* Internet connectivity

___

<a name="installation-procedure"/>
### 4. Installation Procedure
Download the Python based Installation Script from Aptus Server to your installation machine. You can do it using 

```
wget www.aptus.com/install/fonb-setup.py
```

Once you have downloaded the file, you can run it using:

```
python fonb-install.py -i
```

This is the most typical scenario for FonB Installation. To check other options, please see the Options/Switches Section.

<a name="script-prompts"/>
#### 4.1. Script Prompts
While installation, the script prompts for some parameters from user. It provides default values enclosed in paranthesys []. While we recommend leaving default values as is by pressing Enter on such prompts, you may go ahead with custom parameters of your own choice provided that you know what you are doing.

Here is the list of these prompts:

* **php-cgi path:** CGI is an interface which tells the webserver how to pass data back and forth to and from an application. You can refer to the [CGI Documentation](http://hoohoo.ncsa.illinois.edu/cgi) for details.
* **FonB Server Port[8080]:** Server port where you want to run FonB Web Frontend.
* **Mysql username[root]:** Username for your Asterisk MySQL CDR Database. Please check /etc/asterisk/mysql_cdr.conf if you don't know about root credentials on MySQL.
* **Password:** Password for your Asterisk MySQL CDR Database.
* **Database Name[fonb]:** Database name for your Asterisk MySQL CDR. In most FreePBX based systems it's asteriskcdrdb. Check /etc/asterisk/mysql_cdr.conf
* **Hostname[localhost]:** Hostname of machine running MySQL.
* **Extension:** One of the currently configured extension to login with after successful installation.
* **Terminal[SIP/3002]:** Terminal details (we recommend using defaults until and unless you’re aware of the type).
* **Context[from-internal]:** Context of the terminal (we recommend using defaults until and unless you’re aware of the type).
* **Name:** Name you want to give it to this extension in FonB.
* **Mobile:** Mobile Number you want to use in FonB for mobile callback feature (you must have a working trunk to use this feature).
* **Password:** Password for this user to login to FonB Web, Mobile & IP Phone XML App Interface.
* **BaseDir[/usr/local/src/PhoneB]:** Base Directory, used for advanced configuration. Leave it default.
* **Department[Development]:** Department of the user you are creating.
* **Spy[all]:** Specify the departments or extensions you want this user to allow monitoring.
* **Company:** Organization Name.

<a name="options-switches"/>
#### 4.2. Options/Switches
 
* **-h** or **--help** show this help message and exit
* **-i** or **--install** Download and install FonB
* **-s /path/to/PhoneB/bin** or **--init=/path/to/PhoneB/bin** Outputs init.d script. This option expects path to bin folder of phoneb installation to be passed as argument
* **-p php-cgi** or **--php=php-cgi** Validates php requirements for FonB. Expects php-cgi path as argument.
* **-c /path/where/php/will/be/compiled** or **--prefix=/path/where/php/will/be/compiled** Downloads and compiles php with FonB specific requirements.
* **-v** or **--version** Show installation script version
* **-f** or **--freepbx** Install Freepbx module
