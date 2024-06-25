# How to use iptables
## Iptables in details
Used for managing and controlling netfilter (for firewall, NAT, routing...)
Available tables
- Filter table : responsible for filtering incoming/outgoing traffic
- NAT table : responsible for natting private to public address and ports
- Mangle table : responsible for packet modification for routing procedures

For firewall considerations only the filter table will be used
Filter table available chains 
- Input chain : incorming traffic
- Output chain : outgoing traffic
- Forward chain : traffic that is forwarded to another ip
Possible targets
- Accept : allow packet
- Reject : drop packet and send feedback information stating packet was rejected
- Drop : drop and don't send any feedback

## Using iptables
If ufw or firewalld are installed, uninstall them first to have a nice and clean iptables environment

### List rules
```iptables -L```
<br>Note the default policy being used for the tables

### Modify default target for a specific chain
```iptables --policy <chain> <target>```

### Filter connections for a specific ip address
```iptables <chain> <-I for insert the rule to the top or -A for appending to the bottom> -s <source ip or source subnet> -j <target>```
```iptables <chain> -I -p <tcp or udp> --dport <destination port> -s <source ip or subnet> -j <target>```

### Filter connections for a specific port
```iptables -I <chain> -p <tcp or udp> --dport <destination port> -j <target>```

### Filter ICMP requests
```bash
iptables -I <chain> -p icmp -j <target>
iptables -I <chain> -p icmp --icmp-type <any icmp type> -j <target>
```

### Filter only on a specific interface
```iptables -I <chain> -p <port> -i <interface> -j <target>```

### Filter traffic for specific MAC address
```iptables -I <chain> -m mac --mac-source <mac address> -j <target>```

### Filter traffic to a range of port 
```iptables -I <chain> -p <protocol> --dport <1st port>:<last port> -j <target>

### Add a limit on parralel connections
```iptables -I <chain> -p <protocol> --syn --dport <destination port> --conlimit-above <max parralel connections>
### Delete a rule
To delete a rule it is necessary first to list all rule numbers
```iptables -L --line-numbers```
<br> Then it is possible to select the rule number to delete
```iptables -D <chain> <rule number>```

### Save rules to be persistent
```sudo /sbin/iptables-save```

### Flush rules (delete all)
```iptables -F```

## Useful protocols to be allowed depending on the network configuration
SSH port 22 tcp
CUPS port 631 udp/tcp
NTP port 123 udp
SMTP port 25 tcp
DNS port 53 tcp/udp
HTTP port 80 tcp
HTTPS port 443 tcp
POP3 port 110 tcp
IMAP port 143 tcp
Samba port ports 137, 138, 139, 445 tcp

