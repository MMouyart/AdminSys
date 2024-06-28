# Logs
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

Check actual init program location
```readlink -f <init programm location can be found with which init>```

Systemd list units
```systemctl list-unit-files```

Manipulate service
```systemctl <start stop status enable disable reload restart is-active> <service name>```

Check logs
```bash
journalctl # show all journal
journalctl --no-pager # do not use less
journalctl -n 10 # only 10 lines
journalctl -S -1d # last 1 day
journalctl -xe # last few logs
journalctl -u ntp # only ntp unit
journalctl _PID=1234
```
