# Using duplicity to automate backups
To create a full backup
```duplicity full <source directory> <target directory>```

To create an incremental backup (provided you have done a full backup previously)
```duplicity incremental <source directory> <target directory>```

 Verify a backup against non-backed up data
 ```duplicity verify <source directory> <target directory>```

Restoring data using a backup
```duplicity restore <source directory> <target directory>```

Remove data older than specified time
```duplicity remove-older-than <target directory>```

Time specification can be done as follow
```
1. the string "now" (refers to the current time)
2. a sequences of digits, like "123456890" (indicating the time in seconds after the epoch)
3. A string like "2002-01-25T07:00:00+02:00" in datetime format
4. An interval, which is a number followed by one of the characters s, m, h, D, W, M, or Y (indicating seconds, minutes, hours, days, weeks, months, or years respectively), or a series of such pairs. In this case the string refers to the time that preceded the current time by the length of the interval. For instance, "1h78m" indicates the time that was one hour and 78 minutes ago. The calendar here is unsophisticated: a month is always 30 days, a year is always 365 days, and a day is always 86400 seconds.
5. A date format of the form YYYY/MM/DD, YYYY-MM-DD, MM/DD/YYYY, or MM-DD-YYYY, which indicates midnight on the day in question, relative to the current time zone settings. For instance, "2002/3/5", "03-05-2002", and "2002-3-05" all mean March 5th, 2002.
```

Use target directory (containing backup) as follow to :
- speficy a local backup location file:///some_dir
- specify a ftp server backup locationftp[s]://user[:password]@other.host[:port]/some_dir
- specify an rsinc daemon rsync://user[:password]@host.com[:port]::[/]module/some_dir

Including or excluding files can be done by specifying either the file/directory full name or using a pattern/regex with the options
- --include
- --exclude

By default, duplicity does not need to have the words 'full' or 'incremental', it can define what needs to be done based on what backup data is already present.

If you do not want to use a mix of full and incremental commands, you can use the option --full-if-older-than which will do incremental backup until the data is older than what is specified at which time a full backup will be performed.
