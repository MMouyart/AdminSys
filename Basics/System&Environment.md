# How to manage and interact with the systemd and its environment
## System information
```bash
Show info about system with uname
-s      Print the kernel name. This is the default if no option is specified.
-n      Print the nodename or hostname.
-r      Print the release of the kernel. This option is often used with module-handling commands.
-v      Print the version of the kernel.
-m      Print the machine's hardware (CPU) name.
-o      Print the operating system name.
-a      Print all of the above information.
```

Check current date and calendar
```bash
date
cal
timedatectl
```

Select a timezone
```tzselect```

Timezone infos are located in /usr/share/zoneinfo/ (the file /etc/localtime is linked to one of these timezone files)

To check current langage
```locale```

Use iconv to convert file using specific text format to another format
```bash
iconv -f <file format> <format to be encoded to> -t <file>
iconv -l (lists all available formats)
```

Set localtime on the hardware clock
```hwclock --localtime --set --date="01/05/2023 22:04:00"```

Restore to UTC time
```hwclock -u -w```

Set systemclock with ntp
```ntpdate pool.ntp.org```

Ntpd to automatically sync clock
```conf file       /etc/ntp.conf```

Query ntpd
```ntpq -p```

Chrony (newer ntp implementation) to automatically sync clock
```conf file       /etc/chrony/chrony.conf```

Query chrony
```chronyc tracking```

Query chrony in interactive cli
```chronyc```

## Environment related commands
Check current shell
```bash
echo $SHELL
readlink /bin/sh
```

Check ccommand location
```which <cmd>```

Check simple command explanation
```whatis <cmd>```

Check all command locations
```whereis <cmd>```

Characters to be quoted in a command
```* ?[]'"\$;&()|^<>```

Ex : echo '2 \* 3 = 6' <=> 2 * 3 = 6

Escape sequence function
```bash
\a      Alert (bell)
\b      Backspace
\c      Suppress trailing newline (same function as -n option)
\f      Form feed (clear the screen on a video display)
\n      New line
\r      Carriage return
\t      Horizontal tab
```

Show environment variables
```env```

Most used environment variables
```bash
USER    The name of the logged-in user
PATH    List of directories to search for commands, colon separated
EDITOR  Default editor
HISTFILE        Where bash should save its history (normally .bash_history)
HOSTNAME        System hostname
PS1     The Prompt! Play with it
UID     The numeric user id of the logged-in user
HOME    The user's home directory
PWD     The current working directory
SHELL   The name of the shell
$       The process id (or PID of the running bash shell (or other) process
PPID    The process id of the process that started this process (that is, the id of the parent process)
?       The exit code of the last command
```

Command history commands
```bash
Up and Down Arrow       Move in the history
Ctrl+R  Backward Search
Ctrl+O  Run the command you found with Ctrl+R
!!      Run the last command
!10     Run command number 10
!text   search backward for text, and run the first found command
```

Using cat for specific files formats
```bash
bzcat <file.bz>
xzcat <file.xz>
zcat <file.Z>
gzcat <file.gz>
```

## Cups
Check cups status
```systemctl status cups.service```

Configure cups
```bash
ls /etc/cups/
cat /etc/cupsd.conf
cat /etc/cups/printers.conf
```
Check cups logs
```bash
cat /var/log/cups/access_log
cat /var/log/cups/error_log
```

Administer cups on the web
```open https://127.0.0.1:631```

Administration sections and usage
```bash
Section         Usage
Administration  Adding printers, managing jobs and configuring the CUPS server
Jobs            Checking the active, pending & completed jobs
Printers        List or search in the installed printers
```

Legacy cups tools (used under bsd)
```bash
command         usage
lpr             print a file
lpq             show print queue/jobs
lprm            rm/remove a file from priner queue
lpc             printer control / troubleshooting program
```
