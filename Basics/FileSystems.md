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
```

## Files and folders permissions
Permissions symbolic
```bash
rwx     7
rw-     6
r-x     5
r--     4
-wx     3
-w-     2
--x     1
---     0
```

File attributes (only supported by ext family, XFS, ReiserFS, JFS and OCFS2)
```bash
a      A file with the 'a' attribute set can only be opened in append mode for writing.
A      When a file with the 'A' attribute set is accessed, its atime record is not modified.
c      A file with the 'c' attribute set is automatically compressed on the disk by the kernel.
C      A file with the 'C' attribute set will not be subject to copy-on-write updates.  This flag is only supported on file  systems  which  perform  copy-on-write.
d      A file with the 'd' attribute set is not a candidate for backup when the dump program is run.
D      When  a  directory with the 'D' attribute set is modified, the changes are written synchronously to the disk.
e      The 'e' attribute indicates that the file is using extents for mapping the blocks on disk.
E      A file, directory, or symlink with the 'E' attribute set is encrypted by the file system.
F      A  directory  with  the  'F'  attribute  set  indicates that all the path lookups inside that directory are made in a case-insensitive fashion.
i      A file with the 'i' attribute cannot be modified: it cannot be deleted or renamed, no link can be created to this file, most of the file's metadata can  not  be  modified, and the file can not be opened in write mode.
I      The  'I'  attribute  is used by the htree code to indicate that a directory is being indexed using hashed trees.
j      A file with the 'j' attribute has all of its data written to the ext3 or ext4 journal before being written to the file itself.
m      A file with the 'm' attribute is excluded from compression on file systems that support per-file compression.
N      A file with the 'N' attribute set indicates that the file has data stored inline, within the inode itself.
P      A directory with the 'P' attribute set will enforce a hierarchical structure for project id's.
s      When  a file with the 's' attribute set is deleted, its blocks are zeroed and written back to the disk.
S      When a file with the 'S' attribute set is modified, the changes are written synchronously to the disk.
t      A file with the 't' attribute will not have a partial block fragment at the end of the file merged with other files (for those file systems  which  support  tail-merging).
T      A directory with the 'T' attribute will be deemed to be the top of directory hierarchies for the purposes of the Orlov block allocator.
u      When  a  file  with the 'u' attribute set is deleted, its contents are saved.
x      A file with the 'x' requests the use of direct access (dax) mode, if the kernel supports DAX.
V      A file with the 'V' attribute set has fs-verity enabled.  It cannot be written to, and the file system will automatically verify all data read from it  against  a  crypto‐
              graphic  hash  that covers the entire file's contents, e.g. via a Merkle tree.
```

Permissions meanings
```bash
r       read
w       write
x       execute
```

Add specific permission (r, w and x) to group or owner or others
```bash
to owner        chmod u+<permission(s)>
to group        chmod g+<permission(s)>
to other        chmod o+<permission(s)>
```

Remove specific permission (r, w and x) to group or owner or other
```bash
to owner        chmod u-<permission(s)>
to group        chmod g-<permission(s)>
to other        chmod o-<permission(s)>
```

Add or remove file attributes
```bash
chattr +<attribute> <file>
chattr -<file attribute> <file>
```

List file attributes
```lsattr```

Change ownership of a file or directory (use -R to recursively change ownership of all files inside directory)
```chown <owner>:<group owner> <file or directory>```

Change group of a file or directory
```chgrp <group> <file or directory>```

Additionnal permissions
```bash
access mode     octal   symbolic
suid            4000    u+s
sgid            2000    g+s
sticky          1000    +t

Check what default permission will be given for a newly created file or directory
```umask```
ex : umask = 002 => permissions = 664

Change umask (you can add/remove permission just like for permissions)
```bash
umask u=<permissions>,g=<permissions>,o=<permissions>
umask <digit mask>
```

## Soft and hard links
Create hard link
```ln <file or directory to be linked> <new link>```

Create a soft link
```ln -s <file or directory to be linked> <new link>```

Remove a link
```bash
unlink <existing link>
unlink <file or directory being linked (only for soft link)>
```
