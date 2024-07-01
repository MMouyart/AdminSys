# using restic to manage backups
Restic works by using specific folders called repositories.

To initiate a repository
```restic init --repo <repository full path```

To initiate a repository using sftp
```restic -r sftp:user@host:<repository full path> init```

To backup files on a initialised repository
```restic -r <path to repository> backup <files to backup>```

Use the --exlude to exclude a file/folder.

To list all snapshots (i.e. backups iterations) in a repository
```restic -r <path to the repository> snapshots```

To list what is contained in a snapshot
```restic ls <snapshot id>```

To copy snapshots between repositories
```restic -r <destination repository> copy --from-repo <source repository>```

Removing files from a snapshot
```restic -r <repository path> rewrite --exclude <file name>```

Checking the repository integrity
```restic -r <repository path> check```

Restoring from the latest snapshot
```restic -r <repository path> restore latest --target <path to restoration point>```

Restoring from a snapshot
```restic -r <repository path> restore <snapshot id> --target <path to restoration point>```

Restoring from a snapshot and mounting it to a file system
```restic -r <repository path> mount <destintion mount point>```

Removing one snapshot from a repository
```restic -r <repository path> forget <snapshot id>```

Clean data from removed snapshots
```restic -r <repository path> prune```

Automate this 2 steps process
```restic forget --keep-last <snapshot to keep> --prune```

How to automate restic tasks with repository password requirement :
- export the password use the variable "RESTIC_PASSWORD"
- store the password in a file and load it in a restic command with "-p <file path>"
