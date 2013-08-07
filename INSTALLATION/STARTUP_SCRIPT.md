Once you are done with installation and you are able to run FonB manually without any errors, it becomes handy to create a Startup Script under `/etc/init.d` in order to be able to start, stop, restart and check the status of FonB as a background service. The script has been written considering simplicity and user friendliness as major objectives.

To make it happen, you must have root privileges. Being root, you will have to create the script using `vi /etc/init.d/phoneb` with the following script entry:

```
#!/bin/bash
# phoneb daemon
# chkconfig: 345 20 80
# description: phoneb daemon
# processname: phoneb

DAEMON_PATH="/usr/local/PhoneB/bin"                     #Path Location of phoneb executable

DAEMON="phoneb"

NAME="phoneb"
DESC="This Demon controls FonB"
PIDFILE="/var/run/$NAME.pid"
SCRIPTNAME="/etc/init.d/$NAME"

case "$1" in
start)
        printf "%-50s" "Starting $NAME..."
        PID=`$DAEMON_PATH/$DAEMON  >> /var/log/messages 2>&1 & echo $!`     #for Ubuntu 11.04 or above, use
                                                                            #/var/log/syslog instead
        echo "$DAEMON > /dev/null 2>&1 & echo $!"
        echo "Saving PID" $PID " to " $PIDFILE
        if [ -z $PID ]; then
            printf "%s\n" "Fail"
        else
            echo $PID > $PIDFILE
            printf "%s\n" "Ok"
        fi
;;
status)
        printf "%-50s" "Checking $NAME..."
        if [ -f $PIDFILE ]; then
            PID=`cat $PIDFILE`
            if [ -z "`ps axf | grep ${PID} | grep -v grep`" ]; then
                printf "%s\n" "Process dead but pidfile exists"
            else
                echo "Running"
            fi
        else
            printf "%s\n" "Service not running"
        fi
;;
stop)
        printf "%-50s" "Stopping $NAME"
            PID=`cat $PIDFILE`
            cd $DAEMON_PATH
        if [ -f $PIDFILE ]; then
            kill -HUP $PID
            printf "%s\n" "Ok"
            rm -f $PIDFILE
        else
            printf "%s\n" "pidfile not found"
        fi
;;

restart)
        $0 stop
        $0 start
;;

*)
        echo "Usage: $0 {status|start|stop|restart}"
        exit 1
esac
```
Don't forget to perform `chmod +x /etc/init.d/phoneb`. Error messages can be seen in `/var/log/messages`.
