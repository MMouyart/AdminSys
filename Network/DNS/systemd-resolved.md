# Using systemd-resolved for dns resolutions
If your system is using systemd then systemd-resolved is installed.

## Configuring systemd
To configure local dns settings with systemd-resolved you must modify the configuration file (note that if any mistakes are made the file can be deleted to return to normal)
```bash
sudo <editor of your choice> /etc/systemd/resolved.conf
# configure the dns resolvers add the following line
DNS=<ip of dns resolver 1> <ip of dns resolver 2> ... <ip of dns resolver n>
# if you wish to add fallback dns resolver (in case the primary fails), add the following line
FallbackDNS=<ip of fallback dns resolver 1> <ip of fallback dns resolver 2> ... <ip of fallback dns resolver n>
# to use llmnr edit this line
LLMNR=yes
# to use mdns edit this line
MDNS=yes
# to use dnssec edit this line
DNSSEC=yes
# to use dnssec but allow for non dnssec queries edit this line
DNSSEC=allow-downgrade
# to use dns over tls (dot) edit this line
DNSOverTLS=yes
# to use dns over tls but allow non encrypted queries for dns servers that do not support it, edit this line
DNSOverTLS=opportunistic
# to allow dns caching edit this line
Cache=yes
# to enable the stub resolver (which by default is at 127.0.0.53) to propagate the systemd-resolved to all clients, edit this line
DNSStubListener=yes
# to add other stub resolver, edit this line
DNSStubListenerExtra=<ip of stub listener 1> <ip of stub listener 2> ... <ip of stub listener n>
# The syntax allows for various list as follow
DNSStubListenerExtra=192.168.10.10
DNSStubListenerExtra=2001:db8:0:f102::10
DNSStubListenerExtra=192.168.10.11:9953
DNSStubListenerExtra=[2001:db8:0:f102::11]:9953
DNSStubListenerExtra=tcp:192.168.10.12
DNSStubListenerExtra=udp:2001:db8:0:f102::12
DNSStubListenerExtra=tcp:192.168.10.13:9953
DNSStubListenerExtra=udp:[2001:db8:0:f102::13]:9953
# to allow the service to read /etc/hosts edit this line
ReadEtcHosts=yes
# to disable the forwarding of local hostnames edit this line
ResolveUnicastSingleLabel=no
# save and quit
# restart the service
sudo systemctl restart systemd-resolved.service
```
