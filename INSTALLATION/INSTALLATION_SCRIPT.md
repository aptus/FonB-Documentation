##Introduction
Starting from v1.1.0, FonB is now supported by an installation script to automate most of the steps involved in FonB Installation. While the documentation for manual install remains the same, this guide will show how to use the installation script.

The operation of the script is fairly easy, however, please remember that you should have your MySQL credentials with you, as these are the only login name password pairs that you need to provide manually. Rest (other then few prompts) Installation Script handles by its own.

First step is to download the Python Script from Aptus Server to your installation machine. You can do it using `wget www.aptus.com/install/fonb-setup.py`

Once you have downloaded the file, you can run it using:

```
python fonb-install.py -i
```

##Options/Switches
 
-h, --help show this help message and exit

-i, --install Download and install FonB

-s /path/to/PhoneB/bin, --init=/path/to/PhoneB/bin Outputs init.d script. This option expects path to bin folder of phoneb installation to be passed as argument

-p php-cgi, --php=php-cgi Validates php requirements for FonB. Expects php-cgi path as argument.

-c /path/where/php/will/be/compiled, --prefix=/path/where/php/will/be/compiled Downloads and compiles php with FonB specific requirements.

-v, --version Show installation script version

-f, --freepbx Install Freepbx module

##Requirements

* Python 2.x (Tested on Python 2.4, 2.5, 2.6 and 2.7)
* CentOS (Freepbx/Elastix/Stock Asterisks) (Recommended)
* Should run on any linux installation with yum. Installation will go smooth in other linux distroes too, but you will have to resolve dependencies on your own)
* Internet connectivity

##Script Prompts
While installation, the script prompts for some parameters from user. It provides default values enclosed in paranthesys []. While we recommend leaving default values as is by pressing Enter on such prompts, you may go ahead with custom parameters of your own choice provided that you know what you are doing.

Here is the list of these prompts:

* php-cgi path: CGI is an interface which tells the webserver how to pass data back and forth to and from an application. You can refer to the CGI specification (at http://hoohoo.ncsa.illinois.edu/cgi) for details.
* FonB Server Port[8080]: Server port where you want to run FonB Web Frontend.
* Mysql username[root]: Username for your Asterisk MySQL CDR Database. Please check /etc/asterisk/mysql_cdr.conf if you don't know about root credentials on MySQL.
* Password: Password for your Asterisk MySQL CDR Database.
* Database Name[fonb]: Database name for your Asterisk MySQL CDR. In most FreePBX based systems it's asteriskcdrdb. Check /etc/asterisk/mysql_cdr.conf
* Hostname[localhost]: Hostname of machine running MySQL.
* Extension: One of the currently configured extension to login with after successful installation.
* Terminal[SIP/3002]: Terminal details (we recommend using defaults until and unless you’re aware of the type).
* Context[from-internal]: Context of the terminal (we recommend using defaults until and unless you’re aware of the type).
* Name: Name you want to give it to this extension in FonB.
* Mobile: Mobile Number you want to use in FonB for mobile callback feature (you must have a working trunk to use this feature).
* Password: Password for this user to login to FonB Web, Mobile & IP Phone XML App Interface.
* BaseDir[/usr/local/src/PhoneB]: Base Directory, used for advanced configuration. Leave it default.
* Department[Development]: Department of the user you are creating.
* Spy[all]: Specify the departments or extensions you want this user to allow monitoring.
* Company: Organization Name.

