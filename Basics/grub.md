# Grub
## Basic grub commands
Update grub config file
```bash
grub2-mkconfig > /boot/grub2/grub.conf
grub2-mkconfig -o /boot/grub2/grub.cfg
update-grub2
```
Modify Linux kernel in grub command line
```linux <drive number and partition number><kernel path> root =/dev/<drive number> ro quiet```
Ex : linux (hd0, 1)/boot/vmlinuz root=/dev/sd1 ro quiet

Modify initramfs in grub command line
```initrd <drive number and partition><initramfs path>```
Ex : initrd (hd0, 1)/boot/initramfs.img

## Repairing corrupt grub partition
In the case of Grub partition being corrupted but the computer is running (i.e. if rebooted it will crash but it won't crash as long as it stays powered on).
<br>
View which partition Grub is installed on :
```bash
df -h
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sr0     11:0    1 1024M  0 rom  
zram0  251:0    0  2.8G  0 disk [SWAP]
vda    252:0    0   20G  0 disk 
├─vda1 252:1    0    2M  0 part /boot
├─vda2 252:2    0    1G  0 part /
└─vda3 252:3    0   19G  0 part /home
```
Next delete the partition Grub is installed on (/dev/sda1 in this case) using any utility you feel comfortable with (I use fdisk).
<br>
Create a new partition with the correct format which Grub will be installed on.
<br>
Reinstall Grub
```bash
sudo grub2-install /dev/sda #or any volume which has a partition for Grub
```
If everything is right reboot. If something failed then... crap
