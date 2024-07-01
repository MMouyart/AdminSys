# Logs
## Kernel ring buffer 
View kernel ring buffer messages
```bash
dmesg
dmesg | less
cat /var/log/dmesg
```

View kernel logs
```journalctl -k```

View initialisation messages (in systemd based systems)
```journalctl --list-boots```

View initialisation messages from previous boots
```journalctl -b -1```

View init program location
```which init```

View init program name
```ps -p 1```

## Journalctl
Querying journald using journalctl
```journalctl```

Useful journalctl options
```bash
-r              show in reverse; newer on top
-f              keep showing the tail
-e              show and go to the end
-n              show this many lines from the end (newest ones)
-k              show kernel message (equal to dmesg)
-b              show from a specific boot, -1 is the previous one, 0 is the current one. You can check the list using --list-boots
-p              from specific priority, say -p err
-u              from a specific systemd unit
```

Useful commands 
```bash
journalctl # show all journal
journalctl --no-pager # do not use less
journalctl -n 10 # only 10 lines
journalctl -S -1d # last 1 day
journalctl -xe # last few logs
journalctl -u ntp # only ntp unit
journalctl _PID=1234
```

Using journalctl to manually do some cleaning
```bash
--vacuum-time           clean everything older than this. for example --vacuum-time=3months clean anything older than 3 months. You can use s, m, h for seconds, minutes and days and d/days, months, weeks/w and years/y.
--vacuum-size           eliminate till logs occupy a specific size; say 1G
--vacuum-files          Only keep this much archive files
```

## Logrotate
Check logrotate configuration
```cat /etc/logrotate.conf```

Check logrotate for specific services
```cat /etc/logrotate.d/<service>
        
Logrotate parameters meaning
```bash
parameter                       meaning
weekly                          rotate logs weekly
missingok                       it is fine if there is no log for this week
rotate 52                       keep the latest 52 logs and delete the older ones
compress                        compress the logs
create 0640 www-data adm        create the files with this access and owners
pre & post rotate               run these scripts or commands before and after the rotation
```

## General logs
Some famous logs
```bash
/var/log/auth.log       In debian for authentication processes
/var/log/syslog         Centralized place for most of the logs received by rsyslogd
/var/log/kern.log       Kernel messages
/var/run/utmp /var/log/utmp     Successful logins
/var/logs/messages      Informative messages from services
/var/log/btmp           Failed logins
/var/log/faillog        Failed authentication
/var/log/lastlog        Date and time of recent user login
/var/log/<service>/     Logs for every service running and logging
```

Rsyslog
```bash
Modules                 used modules
global directives       general configurations
Rules                   actions, priorities, etc to tell rsyslog what to do
```

Use logger to create a log sent to rsyslog
```logger <log message>```

Check rsyslog contents
```cat /var/log/rsyslog```

## Journald
Check service status    
```systemctl status systemd-journald```

Check config file
```cat /etc/systemd/journald.conf```

Create a log sent to journald
```bash
echo "log message" | systemd-cat
systemd-cat <command>
```

Journald storage management in /etc/systemd/journald.conf
```bash
Variable Name           Usage
SystemMaxUse            The Maximum disk usage, say 500M. The default is on 10%
SystemKeepFree          Keep at least this much free, say 1G. The default is 15%
SystemMaxFileSize       Maximum size of each individual file. The default is 1/8 of SystemMaxUse
SystemMaxFiles          Max number of non-currently-active files. The default is 100
```


## Crash logs
Opening logs of a crashed machine
Obtain machine-id stored in file
```/etc/machine-id```

Check the logs in ```/var/log/journal/<machine-id>```
