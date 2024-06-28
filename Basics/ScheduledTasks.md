# How to schedule tasks
## Cron
Crontab usage
```bash
Sample  A    B    C    D    E    command and arguments
Field   Meaning         Values
A       minute          0-59
B       hour            0-23
C       day of month    1-31
D       month           1-12 (or names, see below)
E       day of week     0-7 (0 or 7 is Sunday, or use names)
```

List crontab jobs
```crontab -l```

Edit crontab jobs
```crontab -e```

## At
Execute a command with at specified time
```at <time>```

ex
```bash
at now + 2 min
at tomorrow
at teatime
```

Check at queued commands
```atq```
Delete at command
```atrm <command number>```

Edit /etc/cron.allow cron.deny /etc/at.allow .deny to allow/deny users to run cron/at commands
```bash
.allow          ONLY users mentioned in this file are allowed to run this service. All other users will be denied
.deny           Everybody can use the service except the users mentioned in this file
```

## Systemd
Systemd timer syntax
```OnCalendar=DayOfWeek Year-Month-Day Hour:Minute:Second```

Another option is to use time shortcut instead of cron like time syntax
```bash
shortcut        when it runs
hourly          beginning of each hour
daily           One a day at midnight
weekly          midnight on Monday
monthly         midnight on the 1st of each month
yearly          midnight on the 1st of Jan
```

Systemd timer monotonic configurations
```config  meaning
OnActiveSec             time after unit activation
OnBootSec               time after system boot
OnStartupSec            time after the service manager is started. For system services, this is almost equal to OnActiveSec. Use this for user services where the service manager is started at user login.
OnUnitActiveSec         time after the corresponding service was last activated
OnUnitInactiveSec       time after the corresponding service was last deactivated
```

It is important to enable and start timer that has been edited
```bash
systemctl enable myservice.timer
systemctl start myservice.timer
```

It is also crucial to enter the following command after every service modification
```systemctl daemon-reload```

Check all scheduled services
```systemctl list-timers```

Use systemd to run a command at specified time in future
```bash
ex systemd-run --on-active="2hours" --unit="helloworld.service"
ex sudo systemd-run --on-active="2hours" /usr/local/bin/helloworld.sh --language=en_US
```
