##Tablt of Contents

####1. [x86_64 glibc Error](#x8664error)

####2. [Google Contacts Not Synching](#googlesyncerror)

####3. [Port 80 In use](#port80inuse)

####4. [PHP Compilation for Elastix Users](#phpcompilation)

####5. [MySQL Old Insecure Authentication Error](#mysqlautherror)
___

<a name="x8664error"/>
###x86_64 glibc Error 

For any error like:

```
-bash: ./phoneb: /lib/ld-linux.so.2: bad ELF interpreter: No such file or directory
```

Try to install glibc libraries using:
```
yum install glibc.i686
```

<a name="googlesyncerror"/>
###Google Contacts Not Synchronizing

Make sure you have installed php-xml package.


<a name="port80inuse"/>
###Port 80 In Use
If you are running any Web based GUI for Asterisk, like Elystix, PIAF or FreePBX, you are going to face this problem as all of such WebGUIs use port 80 for user interface. In such case you can use port 8080 by specifying it in `phoneb.cfg`.


<a name="phpcompilation"/>
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
yum install libxml2 libxml2-devel autoconf automake libtool re2c flex bison openssl-devel
```

####4. ./configure:

Navigate to the decompressed directory for PHP using `cd php-5.4.17` and perform configure step using:

```
./configure --prefix=/usr/local/php --with-mysql --with-openssl
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

<a name="mysqlautherror"/>
###5. MySQL Old Insecure Authentication Error
Use this solution for the problem where you are using any version of MySQL older than 4.1. You may get error like:

```
Warning: [obfuscated](): mysqlnd cannot connect to MySQL 4.1+ using the old insecure authentication. Please use an administration tool to reset your password with the command SET PASSWORD = PASSWORD('your_existing_password'). This will store a new, and more secure, hash value in mysql.user. If this user is used in other scripts executed by PHP 5.2 or earlier you might need to remove the old-passwords flag from your my.cnf file in /usr/local/PhoneB/php/classes/preload.inc on line 0 Cannot connect a database
```

To resolve this issue, you need to re-create your passwords with new authentication. Login to your MySQL using:

```
mysql -u root -p

```
Provide your password anc continue with the following commands one by one:

```
SELECT user, Length(Password) FROM mysql.user;
```
You will get result somewhat like:

```

+----------+--------------------+
| user     | Length(`Password`) |
+----------+--------------------+
| root     |                 41 |
| root     |                 16 |
| user2    |                 16 |
| user2    |                 16 |
+----------+--------------------+
```

You will get the aforementioned error using any password of length 16. To change the length to 41, commit this command for each username:

```
UPDATE mysql.user SET Password = PASSWORD('abcdef') WHERE user = 'user2';

```

Replace `abcdef` with your password and `user2` with the username. Repeat this step for each user with length 16. Once done, repeat the following command to confirm password length:

```
SELECT user, Length(Password) FROM mysql.user;
```

Flush user previliges using:

```
FLUSH PRIVILEGES;
```

Try running FonB again, this time you won't be getting the error.
