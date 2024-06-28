# Using the dnsmasq service to resolve local dns queries

## Install dnsmasq
If dnsmasq is not installed on the system, use your packet manager to download and install it.

Once installed, if your are using systemd, enable it, and disable systemd-resolved
```bash
sudo systmectl enable dnsmasq.service
sudo systemctl restart dnsmasq.service
sudo systemctl disable systemd-resolved.service
sudo systemctl stop systemd-resolved.service
```

## Edit the dnsmasq configuration file
```bash
sudo <editor of your choice> /etc/dnsmasq.conf
# uncomment the following lines
domain-needed
bogus-priv
# these lines will filter dns requests and stop forwarding local hostnames
# if you want to use dnssec validation uncomment those 2 lines
conf-file=/usr/share/dnsmasq/trust-anchors.conf
dnssec
# if you want dnsmasq to trust unsigned dnssec replies but still verify that the source is legitimate uncomment this line
dnssec-check-unsigned
# if you do not want dnsmasq to read /etc/resolv.conf uncomment the following line
no-resolv
# add the following line to specify which resolver you want dnsmasq to use
server=<ip adress of the resolver>
# if you want to specify a local domain not be resolved by dns queries (only using locally defined files) uncomment this line
local=/<local domain>/
# save and quit
# restart the service
sudo systemctl restart dnsmasq.service
```
