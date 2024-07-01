# File systems management
## File System Hierarchy (FHS)
```bash
directory       usage
/               Primary hierarchy root and root directory of the entire file system hierarchy
/bin            Essential command binaries
/boot           Static files of the boot loader
/dev            Device files
/etc            Host-specific system configuration
/lib            Essential shared libraries and kernel modules
/media          Mount point for removable media
/mnt            Mount point for mounting a filesystem temporarily
/opt            Add-on application software packages
/sbin           Essential system binaries
/srv            Data for services provided by this system
/tmp            Temporary files
/usr            Secondary hierarchy
/var            Variable data
/home           User home directories (optional)
/lib            Alternate format essential shared libraries (optional)
/root           Home directory for the root user (optional)
```

Mount a file system on an existing directory
```bash
mount <file system> <mount point>
mount -t <file system type> <file system> <mount point>
mount uuid=<file system uuid> <mount point>
```

Unmount a file system
```bash
umount <file system>
```bash
umount <mount point>
```

Check file systems uuids
```bash
lsblk -o +UUID
blkid
```

## Fstab to add a file system to be mounted automatically

Fstab columns
```bash
file system:    Label, UUID, device
mount point:    swap or none for swap
type:           can be ext4 , xfs ,nfs or other types of filesystem
options:        defaults, rw / ro, noauto, user, exec / noexec, noatime, umask
dump:           do dump command backup this? mostly 0
pass:           Non-zero values of pass specify the order of checking filesystems at boot time
```

## File system related commands
Use df to show disk usage
```bash
df -h   shows in human readable
df -T   shows file system types
df -i   shows counts in inodes and not in size
```

Use du on directories to check their disk usage

Common du options
```bash
-h      print sizes in powers of 1024 (e.g., 1023M)
-H      print sizes in powers of 1000 (e.g., 1.1G)
-c      show the grand total
--max-depth 2   Calculate all but show only 2 directories deep
-s      Only shows the summary and not all the directories one by one
```

Use fsck to fix corrupted file systems
```bash
fsck  <file system path>
fsck  UUID="<file system uuid>" (uuid can be found using blkid)
```

File systems types and commmands to check integrity and repair
```bash
Type    Command         Usage
ext     tune2fs         Show or set ext2 and ext3 parameters or even set the journaling options
ext     dumpe2fs        Prints the super block and block group descriptor information for an ext2 or ext3 filesystem.
ext     debugfs         Is an interactive file system debugger. Use it to examine or change the state of an ext2 or ext3file system.
reiserfs        reiserfstune            show and set parameters
reiserfs        debugreiserfs           Prints the super block and block group descriptor information for an ext2 or ext3 filesystem.
XFS     xfs_info        display information
XFS     xfs_growfs      expand file system
XFS     xfs_admin       change parameters on XFS file systems
XFS     xfs_repair      repair the problems
XFS     xfs_db          checks and debugs the filesystem
```bash
