# Rotating logs with logrotate
The purpose of log rotate if to manage logs in their lifetime, to avoid having one enormous log file that never gets used because it is too heavy.

By specifying intervals of conditions for log files to be rotated (i.e. being compressed, archived, sent via emails...), it is easier to keep a nice time separated and organised log architecture.

Logrotate store its configuration under /etc/logrotate.conf

To ensure logrotates really rotates log files, it should either be placed in a crontab (running every day or more) or be ran manually with the command ```logrotate <path to the configuration file```.

Logrotate has a global configuration file under /etc/logrotate.conf which defines configurations that are made globally on the system no matter what logs are being rotated.

Under /etc/logrotate.d/ all files define specific configurations for each log files (note that they overwrite global configurations).

Example of /etc/logrotate.d/dpkg: 
```bash
/var/log/dpkg.log {
	monthly
	rotate 12
	compress
	missingok
	notifempty
}
```
The above example states that the log file dpkg.log located under /var/log/dpkg.log, should be rotated monthly, after a 12 months period, the logs file should be compressed (using gzip by default), it will not stop on errors, and it will not happen if the log file is empty.

## Log file selection
The configuration file first defines logs that should be treated, which can either be in their full name or in a regex pattern.

Examples :
- /var/log/httpd --> the file https under /var/log
- /var/log/*log --> all files ending with "log"
- /var/log/^http --> all files starting with "http"

## Rotation frequency
These options specify the rotation frequency :
- weekly
- daily
- yearly
- monthly

These options are followed by the option rotate and a value.

Examples :
- rotate every 2 months
```bash
/var/log/dpkg.log {
  monthly
  rotate 2
```
- rotate every 5 days
```bash
/var/log/dpkg.log {
  daily
  rotate 5
```

Or instead of a time basis, the configuration file can work on a size basis, working by using the word size followed by a size in K, M or G

Examples : 
- size 100K
- size 250M
- size 10G

It is also possible to combine those 2 criterias.
Examples : 
- rotate every 2 weaks or if the log file is bigger than 10G
```bash
/var/log/dpkg.log {
  weakly
  rotate 2
  size 10G
```

In addition to that it is possible to specify under which user and group the log rotation should be made (if the specified user & group does not have sufficient privilege to perform the rotation it will cause errors).

Examples : 
- rotation performed as user daemon in group rotate
```bash
/var/log/dpkg.log {
  su daemon rotate
```
- rotation performed as user root in group log
```bash
/var/log/dpkg.log {
  su root log
```

## File based options
These options allow to add rules on whether or not to perform the ration based on the log file attributes.
- **missingok**
              If the log file is missing, go on to the next one without issuing an error message.  See also nomissingok.
- **nomissingok**
              If a log file does not exist, issue an error.  This is the default.
- **ignoreduplicates**
              Ignore any following matches of a log file.
- **ifempty**
              Rotate the log file even if it is empty, overriding the notifempty option (ifempty is the default).
- **notifempty**
              Do not rotate the log if it is empty (this overrides the ifempty option).
- **minage <count>**
              Do not rotate logs which are less than <count> days old.
- **maxage <count>**
              Remove rotated logs older than <count> days. The age is only checked if the logfile is to be rotated.- minsize size
              Log files are rotated when they grow bigger than size bytes, but not before the additionally specified time interval (daily, weekly, monthly, or yearly).- maxsize size
              Log files are rotated when they grow bigger than size bytes even before the additionally specified time interval (daily, weekly, monthly, or yearly).
## Actions
These options specify what to do when in the rotation phase.
- **create mode <owner> <group>, create <owner> <group>**
              Immediately after rotation (before the postrotate script is run) the log file is created (with the same name as the log file just rotated).- nocreate
              New log files are not created (this overrides the create option).
- **createolddir mode <owner> <group>**
              If the directory specified by olddir directive does not exist, it is created.- nocreateolddir
              olddir directory is not created by logrotate when it does not exist.
- **copy**
              Make  a  copy of the log file, but don't change the original at all.- nocopy
              Do not copy the original log file and leave it in place.
- **copytruncate**
              Truncate the original log file to zero size in place after creating a copy, instead of moving the old log file and optionally creating a new one.- nocopytruncate
              Do not truncate the original log file in place after creating a copy.
- **renamecopy**
              Log  file is renamed to temporary filename in the same directory by adding ".tmp" extension to it.- norenamecopy
              Do not rename and copy the original log file (this overrides the renamecopy option).
- **shred**
              Delete  log  files  using  shred  -u  instead of unlink().
- **noshred**
              Do not use shred when deleting old log files.  See also shred.

## Mailing options
These options specify mail related actions to follow when rotating logs.
- **mail <address>**
              When a log is rotated out of existence, it is mailed to address.  If no mail should be generated by a particular log, the nomail directive may be used.
- **nomail**
              Do not mail old log files to any address.
- **mailfirst**
              When using the mail command, mail the just-rotated file, instead of the about-to-expire file.
- **maillast**
              When using the mail command, mail the about-to-expire file, instead of the just-rotated file (this is the default).

## Scripts
These options allow to run scripts before or after rotation.
- **sharedscripts**
              Normally, prerotate and postrotate scripts are run for each log which is rotated and the absolute path to the log file is passed as first argument  to  the  script.
- **firstaction
   script
  endscript**
              The  script is executed once before all log files that match the wildcarded pattern are rotated, before the prerotate script is run and only if at least one log will actually be rotated.- lastaction
- **lastaction
    script
  endscript**
              The script is executed once after all log files that match the wildcarded pattern are rotated, after the postrotate script is run and only if at least one log is  rotated.- prerotate
- **prerotate
    pscript
  endscript**
              The script is executed before the log file is rotated and only if the log will actually be rotated.- postrotate
- **preremove
    script
  endscript**
              The script is executed after the log file is rotated.
