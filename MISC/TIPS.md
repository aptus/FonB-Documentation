##Tablt of Contents

####1. [x86_64 glibc Error](#x8664error)

####2. [Google Contacts Not Synching](#googlesyncerror)

####3. [Port 80 In use](#port80inuse)

####4. [PHP Compilation for Elastix Users](#phpcompilation)
___

<a name="#x8664error">
###x86_64 glibc Error 

For any error like:

```
-bash: ./phoneb: /lib/ld-linux.so.2: bad ELF interpreter: No such file or directory
```

Try to install glibc libraries using:
```
yum install glibc.i686
```

<a name="#googlesyncerror">
###Google Contacts Not Synchronizing

Make sure you have installed php-xml package.


<a name="#port80inuse">
###Port 80 In Use
If you are running any Web based GUI for Asterisk, like Elystix, PIAF or FreePBX, you are going to face this problem as all of such WebGUIs use port 80 for user interface. In such case you can use port 8080 by specifying it in `phoneb.cfg`.


<a name="#phpcompilation">
###PHP Compilation for Elastix Users

Elastix users will need to compile PHP in order to run FonB. The steps to compile are as under:

####1. Download Source:

Download PHP Source in the directory '/usr/src' using command:
```
wget http://www.php.net/get/php-5.4.17.tar.gz/from/ar2.php.net/mirror
```
####2. Decompress The Source:

Decompress the downloaded source using:

```
tar xzf php-5.4.17.tar.gz
```

####3. Install The Dependencies:

Install the required dependencies for PHP Compilation using command:

```
yum install libxml2 libxml2-devel autoconf automake libtool re2c flex bison
```

####4. ./configure:

Navigate to the decompressed directory for PHP using `cd php-5.4.17` and perform configure step using:

```
./configure --prefix=/usr/local/php
```

####5. Compilation & Installation:

Compile and install PHP using these commands one by one:

```
make

make install
```

####6. Configure phone.cfg:

Edit `phoneb.cfg` and add the following:

```
PhpCgiPath = /usr/local/php/bin/php-cgi
```
