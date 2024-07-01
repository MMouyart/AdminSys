# Using the ip command to show/manipulate routing, devices, policy routing and tunnels
## Addresses
Add an ip to an interface
```ip addr add dev <interface> local <ip>```
Note that the address can be followed by a subnet mask in slash notation.

Delete an ip from an interface
```ip addr del dev <interface>```
Note that multiple ips can be associated to an interface, therefore it is need to indicate which one to delete otherwise they will all be deleted.
```ip addr del dev <interface> local <ip>```

