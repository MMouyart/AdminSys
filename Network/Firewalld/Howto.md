# Firewalld
If firewalld is the firewall solution used for the current distro.
## Zones
List all available zones 
```bash
firewall-cmd --get-zones
```
After each change that is made permanent remember to use ```firewall-cmd --reload``` for the change to be effective
Find which one is the current and default one
```bash
firewall-cmd --get-default-zone
```
Change the default zone
```bash
firewall-cmd --set-default-zone=<zone name>
```
View which zones are active (i.e. having one or more interfaces assigned to them)
```bash
firewall-cmd --get-active-zones
```
Assign an interface to a zone
```bash
firewall-cmd --zone=<zone name> --change-interface=<interface name> --permanent
```
Create a new zone
```bash
firewall-cmd --permanent --new-zone=mytest 
```
Check which zone an interface is mapped to
```bash
firewall-cmd --get-zone-of-interface=<interface name>
```
## Ports
Add a rule to allow a specific TCP/UDP port for traffic to flow in
```bash
firewall-cmd --zone=<zone name> --add-port=<port number>/tcp --permanent
firewall-cmd --zone=<zone name> --add-port=<port number>/udp --permanent
```
Check which ports are open
```bash
firewall-cmd --list-ports
```
Remove a port
```bash1
firewall-cmd --remove-port=2020/tcp --permanent
```
## Services
Add a specific service (i.e. which is meant to work with well known ports)
```bash
firewall-cmd --add-service=<service name> --zone=<zone name> --permanent
```
List available services
```bash
firewall-cmd --list-services --zone=<zone name>
```
Remove a specific service
```bash
firewall-cmd --zone=<zone name> --remove-service=<service name> --permanent
```
## Logs
Modify firewalld to log all denied events (logs will be collected at /var/log/messages)
```bash
firewall-cmd --set-log-denied=all
```
## Rich rules
Making a rich (more granular rule)
```bash
firewall-cmd --zone=<zone name> --permanent --add-rich-rule='rule family=<ipv4/ipv6> source address=<source ip> port port=<port number> protocol=<tcp/udp> <reject/accept>
firewall-cmd --zone=<zone name> --permanent --add-rich-rule='rule family=<ipv4/ipv6 with subnet mask> source address=<source ip> port port=<port number> protocol=<tcp/udp> <reject/accept>
```
Create an ipset (grouping multiple ips into one entity)
```bash
firewall-cmd --permanent --ipset=<ipset name> --add-entry=<ip address>
firewall-cmd --permanent --ipset=<ipset name> --add-entry=<ip address>
```
Create a rich rule with an existing ipset
```bash
firewall-cmd --permanent --zone=<zone name> --add-rich-rule='rule source ipset=<ipset name> port port=<port number> protocol=<tcp/udp> <reject/accept>'
```
Remove a rich rule
```bash
firewall-cmd --permanent --zone=<zone name> --remove-rich-rule='rule source ipset=<ipset name> port port=<port number> protocol=<tcp/udp> <reject/accept>'
```
## Forwarding rules
Add a forwarding rule
```bash
firewall-cmd --zone=<zone name> --add-forward-port=port=<incoming port>:proto=<protocol>:toport=<destination port> --permanent
```
Remove a forwarding rule
```bash
firewall-cmd --zone=<zone name> --remove-forward-port=port=<incoming port>:proto=<protocol>:toport=<destination port> --permanent
```
Firewalld man page link : https://firewalld.org/documentation/man-pages/
