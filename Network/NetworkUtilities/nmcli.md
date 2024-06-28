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

# deactivate an active connection
nmcli con down <connection name>

# modify a connection
nmcli con mod <connection name> <settings to change>

# delete a connection
nmcli con del <connection name>

# create a connection
nmcli con add <connection name> ifname <interface to bind the connection to> type <connection type>
# the connection can be one of the available types listed below :
  - 6lowpan
  - 802-11-olpc-mesh (alias olpc-mesh)
  - 802-11-wireless (alias wifi)
  - 802-3-ethernet (alias ethernet)
  - adsl
  - bluetooth
  - bond
  - bond-slave (deprecated for ethernet with master)
  - bridge
  - bridge-slave (deprecated for ethernet with master)
  - cdma
  - dummy
  - generic
  - gsm
  - infiniband
  - ip-tunnel
  - macsec
  - macvlan
  - olpc-mesh
  - ovs-bridge
  - ovs-dpdk
  - ovs-interface
  - ovs-patch
  - ovs-port
  - pppoe
  - team
  - team-slave (deprecated for ethernet with master)
  - tun
  - veth
  - vlan
  - vpn
  - vrf
  - vxlan
  - wifi-p2p
  - wimax
  - wireguard
  - wpan

# modify the dhcp settings of a connection to be automatically acquired
nmcli con mod <connection  name> ipv4.method auto
nmcli con mod <connection  name> ipv6.method auto

# modify the dhcp settings of a connection to be manually assigned
nmcli con mod <connection  name> ipv4.method manual
nmcli con mod <connection  name> ipv6.method manual

# modify the gateway of a connection  
nmcli con mod <connection name> ipv4.gateway
nmcli con mod <connection name> ipv6.gateway

# clone a connection
nmcli con clone <existing connection> <new connection>

# monitor all connections
nmcli con mon

# monitor one specific connection
nmcli con mon <connection>

# reload all connections (needed when they are modified for the changes to take effect)
nmcli con reload
```

## Viewing/modifying devices
```bash
# view all devices status
nmcli dev stat

# show informations about devices
nmcli -p -m multiline dev sh

# show detailed informations about devices
nmcli -p -m multiline -f all dev sh

# show informations about one device
nmcli -p -m multiline dev sh <device>

# show detailed informations about one device
nmcli -p -m multiline -f all dev sh <device>

# set device properties (autoconnect and managed)
nmcli dev set <device> { autoconnect {yes | no} | managed {yes | no}}

# connect a device
nmcli dev up <device>
nmcli dev connect <device>

# update a device configuration (for configuration changes to take effect)
nmcli dev reapply <device>

# modify the setings of a device
nmcli dev mod <device> <settings to modify>

# bring a device down
nmcli dev down <device>
nmcli dev disconnect <device>

# delete a software device
nmcli dev del <device>

# monitor all devices
nmcli dev mon

# monitor one device
nmcli dev mon <device>

# connect to a wifi network
nmcli dev wifi connect <ssid> {password <password> | wep-key-type {key | phrase}}

# create a wifi hotspot
nmcli dev wifi hotspot ssid <hotspot name>

# rescan for available ap
nmcli dev wifi rescan
```
