# Hardware related commands
List connected devices
```lspci```

List details about a specific device
```lspci -s <device ID> -v```

List details about kernel driver for a specific device
```lspci -s <device ID> -k```

List connected USB devices
```lsusb```

List details about a connected USB device
```lsusb -v -d <drevice ID>```

List usb device hierarchy
```lsusb -t```

List loaded kernel modules
```lsmod```

Unload specific module
```modprobe -r <module name>```
Load specific module

```modprobe -p <module name>```

Directories containing hardware infos
```bash
/proc/cpuinfo
/proc/interrupts
/proc/ioports
/proc/dma
```
