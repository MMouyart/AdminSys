# Ufw 
A utility made for ease of iptables rules management
On debian-based distros, ufw should be included by default

## List rules 
```ufw status```
<br> Rules should be listed as : port/protocol, action, from 

## Reset rules
```ufw reset```

## Add a default rule
ufw default <allow/deny> <incoming/outgoing>

## Add a rule for a service (either by name or port) 
ufw <action> <port or service>```

## Add a rule for a service from a specific source/subnet
```ufw <action> <port or service> from <ip source or subnet>```

## Add a rule for a service for a specific destination/subnet
```ufw <action> <service or port> to <ip address or subnet>

## List rules by line number
```ufw status numbered```

## Delete a rule
```ufw delete rule <rule number>```

## Add a rule with transport protocol specification
```ufw <action> protocol <tcp/udp> from <source ip or subnet> to <destination ip or subnet> port <port number>```

## Add rule specifying incoming interface
```ufw <action> incoming on <interface> to <destination> port <port number protocol <transport protocol>

## Add routing/forwarding rules 
```ufw route <action> incoming on <incoming interface> outgoing on <outgoing interface>```
<br> Being more granular by specifying source and destination 
```ufw route <action> incoming on <interface> form <ip address or subnet> outgoing on <interface> to <ip address or subnet> port <port number> protocol <transport protocol>

## Filter ICMP responses
Modify the file under /etc/ufw/before.rules, either comment all the lines or replace the ACCEPT statements by DROP statements

