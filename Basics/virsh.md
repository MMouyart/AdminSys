# Virsh related commands for Vms management
## To connect to virsh :
```bash	
virsh --connect qemu:///system 
```

## Once connected, to list VMs : 
```bash	
virsh list --all
 ```

## To list all networks : 
```bash	
virsh net-list
 ```

## To find info regarding one network : 
```bash	
	virsh net-info <network>
 ```

## To find VM ip address using its associated network :
```bash	
	virsh net-dhcp-leases <network>
 ```

## To start VM : 
```bash	
	virsh start <vm>
 ```

## To stop VM : 
```bash	
	virsh stop <vm>
 ```

## To reboot VM : 
```bash	
	virsh reboot <vm>
 ```

## To force VM stop : 
```bash	
	virsh destroy <vm>
 ```

## To delete VM : 
```bash	
	virsh destroy <vm> 2> /dev/null	
	virsh undefine <vm>
	virsh pool-refresh default
	virsh vol-delete --pool default <vm>.qcow2
 ```

## To completely wipe VM storage : 
```bash	
	rm -rf /var/lib/libvirt/images/<vm>.qcow2
 ```

## To rename VM : 
```bash	
	virsh domrename <oldname> <newname>
 ```

## To edit a VM config file : 
```bash	
	virsh edit <vm>
 ```

## To check VM config : 
```bash	
	virsh dominfo <vm>
 ```

## To automate VM start at host boot : 
```bash	
	virsh autostart <vm>
 ```

## To disable autostart : 
```bash	
	virsh autostart --disable <vm> 
 ```

## To stop all running VMs : 
```bash	
	for i in `sudo virsh list | grep running | awk '{print $2}'` do
	    	sudo virsh shutdown $i
	done
 ```

## To create a VM : 
```bash	
	sudo virt-install \
	--name=<vm name> \
	--description=<ma description> \
	--ram=<ram amount> \
	--vcpus=<vcpu amount> \
	--os-variant=<os> \
	--disk size=<size in GO> \
	--graphics spice \
	--cdrom=<installer image path> \
	--network bridge:<virtual networks interface>
 ```

## To clone a VM (it is needed to shut it down first) : 
```bash	
	virt-clone --original <vm> --auto-clone
	virsh domrename <vm-clone> <new name>
 ```

## To list all possible os variants available : 
```bash	
	virt-install --os-variant list
 ```

## To connect to the console of a VM : 
```bash	
	virsh console <vm>
 ```
If a console is already open, this will fail so it is necessary to force the connection : 
```bash	
	virsh console <vm> --force
 ```

## To save a VM state : 
```bash	
	virsh save <vm> <snapshot path>
 ```

## To restore a VM state from snapshot : 
	virsh restore <snapshot path>
 ```
Virsh man page link : https://linux.die.net/man/1/virsh
