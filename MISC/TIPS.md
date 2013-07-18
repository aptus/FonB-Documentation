###x86_64 glibc Error 

For any error like:

```
-bash: ./phoneb: /lib/ld-linux.so.2: bad ELF interpreter: No such file or directory
```

Try to install glibc libraries using:
```
yum install glibc.i686
```

###Google Contacts Not Synchronizing

Make sure you have installed php-xml package.

###Port 80 In Use
If you are running any Web based GUI for Asterisk, like Elystix, PIAF or FreePBX, you are going to face this problem as all of such WebGUIs use port 80 for user interface. In such case you can use port 8080 by specifying it in `phoneb.cfg`.
