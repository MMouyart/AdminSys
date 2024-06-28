# Interacting with network manager through nmcli
Nmcli is a cli tool used to interact with network manager and modify or view any network configurations on a GNU/Linux machine.

## View/modifying connections
```bash
# check if network manager is running (without using systemctl)
nmcli -t -f STATE general

# check the status of all neetwork related devices on the machine
nmcli dev

# list all connections (active and inactive ones)
nmcli con

# list only active connections
nmcli con sh --active

# show the basic details of every connection
nmcli -p -m multiline con sh

# show all details of every connection
nmcli -p -m multiline -f all con sh

# show the basic details of every active connection
nmcli -p -m multiline con sh --active 

# show all details of every active connection
nmcli -p -m multiline -f all con sh --active

# activate an inactive connection
nmcli con up <connection name>

# delete a connection
nmcli con del <connection name>

# create a connection
nmcli con add <connection name> ifname <interface to bind the connection to> type <connection type>

# modify the dhcp settings of a connection to be automatically acquired
nmcli con mod <connection  name> ipv4.method auto
nmcli con mod <connection  name> ipv6.method auto

# modify the dhcp settings of a connection to be manually assigned
nmcli con mod <connection  name> ipv4.method manual
nmcli con mod <connection  name> ipv6.method manual

# modify the gateway of a connection  
nmcli con mod <connection name> ipv4.gateway
nmcli con mod <connection name> ipv6.gateway

```

## Viewing/modifying devices
