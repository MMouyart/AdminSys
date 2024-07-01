# Using the ip command to show/manipulate routing, devices, policy routing and tunnels
## Addresses
Add an ip to an interface
```ip addr add dev <interface> local <ip>```
Note that the address can be followed by a subnet mask in slash notation.

Delete an ip from an interface
```ip addr del dev <interface>```
Note that multiple ips can be associated to an interface, therefore it is need to indicate which one to delete otherwise they will all be deleted.
```ip addr del dev <interface> local <ip>```

Show address(es) assigned to an interface
```ip addr show dev <interface>```

Delete assigned addresses of an interface according to the specified scope
```ip addr flush dev <interface>```

The available scopes are defined at /etc/iproute2/rt_scopes

Example commands
```bash
Show IPv4 and IPv6 addresses assigned to all network interfaces.
ip address show

Show IPv4 and IPv6 addresses assigned to active network interfaces are shown.
ip address show up

Show IPv4 and IPv6 addresses assigned to network interface eth0.
ip address show dev eth0

Add an IPv6 address to network interface eth1.
ip address add 2001:0db8:85a3::0370:7334/64 dev eth1

Delete the IPv6 address added to network interface eth1.
ip address delete 2001:0db8:85a3::0370:7334/64 dev eth1

Remove all global IPv4 and IPv6 addresses from device eth4.
ip address flush dev eth4 scope global
```

## Link
Add a virtual link
```ip link add link <physical device> name <name of the link> type <type of the link>```

Modify the values of an existing link
```ip link set dev <link> <new values>```

Delete a link
```ip link delete dev <link>```

Show all links
```ip link show```

Only show links of a specific type
```ip link show type <link type>```

The list of available link types is as follow :
- amt - Automatic Multicast Tunneling (AMT)
- bareudp - Bare UDP L3 encapsulation support
- bond - Bonding device
- bridge - Ethernet Bridge device
- can - Controller Area Network
- dsa - Distributed Switch Architecture
- dummy - Dummy network interface
- erspan - Encapsulated Remote SPAN over GRE and IPv4
- geneve - GEneric NEtwork Virtualization Encapsulation
- gre - Virtual tunnel interface GRE over IPv4
- gretap - Virtual L2 tunnel interface GRE over IPv4
- gtp - GPRS Tunneling Protocol
- hsr - High-availability Seamless Redundancy device
- ifb - Intermediate Functional Block device
- ip6erspan - Encapsulated Remote SPAN over GRE and IPv6
- ip6gre - Virtual tunnel interface GRE over IPv6
- ip6gretap - Virtual L2 tunnel interface GRE over IPv6
- ip6tnl - Virtual tunnel interface IPv4|IPv6 over IPv6
- ipip - Virtual tunnel interface IPv4 over IPv4
- ipoib - IP over Infiniband device
- ipvlan - Interface for L3 (IPv6/IPv4) based VLANs
- ipvtap - Interface for L3 (IPv6/IPv4) based VLANs and TAP
- lowpan - Interface for 6LoWPAN (IPv6) over IEEE 802.15.4 / Bluetooth
- macsec - Interface for IEEE 802.1AE MAC Security (MACsec)
- macvlan - Virtual interface base on link layer address (MAC)
- macvtap - Virtual interface based on link layer address (MAC) and TAP.
- netdevsim - Interface for netdev API tests
- nlmon - Netlink monitoring device
- rmnet - Qualcomm rmnet device
- sit - Virtual tunnel interface IPv6 over IPv4
- vcan - Virtual Controller Area Network interface
- veth - Virtual ethernet interface
- virt_wifi - rtnetlink wifi simulation device
- vlan - 802.1q tagged virtual LAN interface
- vrf - Interface for L3 VRF domains
- vti - Virtual tunnel interface
- vxcan - Virtual Controller Area Network tunnel interface
- vxlan - Virtual eXtended LAN
- xfrm - Virtual xfrm interface

Examples : 
```bash
# Show the state of all network interfaces on the system.
ip link show

# Show the bridge devices.
ip link show type bridge

# Show the vlan devices.
ip link show type vlan

# Show devices enslaved by br0
ip link show master br0

# Change the MTU the ppp0 device.
ip link set dev ppp0 mtu 1400

# Create a new vlan device eth0.10 on device eth0.
ip link add link eth0 name eth0.10 type vlan id 10

# Remove vlan device.
ip link delete dev eth0.10

# Create an IPIP that is encapsulated with Generic UDP Encapsulation, and the outer UDP checksum and remote checksum offload are enabled.
ip link add name tun1 type ipip remote 192.168.1.1 local 192.168.1.2 ttl 225 encap gue encap-sport auto encap-dport 5555 encap-csum encap-remcsum

# Attache a XDP/BPF program to device eth0, where the program is located in prog.o, section "prog" (default section). In case a XDP/BPF program is already attached,  throw  an error.
ip link set dev eth0 xdp obj prog.o
```

##  Route
Add a route
```ip route add <destination ip or subnet> via <next hop> dev <interface> <additionnal parameters>```

Add a default route
```ip route ad default via <next hop> dev <interface> <additionnal parameters>```

Show all routes
```ip route```

Show routes to a specific destination
```ip route show to <destination> <additionnal parameters>```

Return a single route to a destination
```ip route get to <destination> <additionnal parameters>```

Save all routes to stdout
```ip route save```

Delete a route
```ip route delete to <destination> <additionnal parameters>```

Additionnal parameters listed below :
- SELECTOR := [ root PREFIX ] [ match PREFIX ] [ exact PREFIX ] [ table TABLE_ID ] [ vrf NAME ] [ proto RTPROTO ] [ type TYPE ] [ scope SCOPE ]
- ROUTE := NODE_SPEC [ INFO_SPEC ]
- NODE_SPEC := [ TYPE ] PREFIX [ tos TOS ] [ table TABLE_ID ] [ proto RTPROTO ] [ scope SCOPE ] [ metric METRIC ] [ ttl-propagate { enabled | disabled } ]
- INFO_SPEC := { NH | nhid ID } OPTIONS FLAGS [ nexthop NH ] ...
- NH := [ encap ENCAP ] [ via [ FAMILY ] ADDRESS ] [ dev STRING ] [ weight NUMBER ] NHFLAGS
- FAMILY := [ inet | inet6 | mpls | bridge | link ]
- OPTIONS := FLAGS [ mtu NUMBER ] [ advmss NUMBER ] [ as [ to ] ADDRESS ] rtt TIME ] [ rttvar TIME ] [ reordering NUMBER ] [ window NUMBER ] [ cwnd NUMBER ] [ ssthresh NUMBER ] [
               realms REALM ] [ rto_min TIME ] [ initcwnd NUMBER ] [ initrwnd NUMBER ] [ features FEATURES ] [ quickack BOOL ] [ congctl NAME ] [ pref PREF ] [ expires TIME ] [
               fastopen_no_cookie BOOL ]
- TYPE := [ unicast | local | broadcast | multicast | throw | unreachable | prohibit | blackhole | nat ]
- TABLE_ID := [ local| main | default | all | NUMBER ]
- SCOPE := [ host | link | global | NUMBER ]
- NHFLAGS := [ onlink | pervasive ]
- RTPROTO := [ kernel | boot | static | NUMBER ]
- FEATURES := [ ecn | ]
- PREF := [ low | medium | high ]
- ENCAP := [ ENCAP_MPLS | ENCAP_IP | ENCAP_BPF | ENCAP_SEG6 | ENCAP_SEG6LOCAL | ENCAP_IOAM6 ]
- ENCAP_MPLS := mpls [ LABEL ] [ ttl TTL ]
- ENCAP_IP := ip id TUNNEL_ID dst REMOTE_IP [ src SRC ] [ tos TOS ] [ ttl TTL ]
- ENCAP_BPF := bpf [ in PROG ] [ out PROG ] [ xmit PROG ] [ headroom SIZE ]
- ENCAP_SEG6 := seg6 mode [ encap | encap.red | inline | l2encap | l2encap.red ] segs SEGMENTS [ hmac KEYID ]
- ENCAP_SEG6LOCAL := seg6local action SEG6_ACTION [ SEG6_ACTION_PARAM ] [ count ]
- ENCAP_IOAM6 := ioam6 [ freq K/N ] mode [ inline | encap | auto ] [ tundst ADDRESS ] trace prealloc type IOAM6_TRACE_TYPE ns IOAM6_NAMESPACE size IOAM6_TRACE_SIZE
- ROUTE_GET_FLAGS :=  [ fibmatch  ]

Examples :
```bash
# Show all route entries in the kernel.
ip ro

# Add a default route (for all addresses) via the local gateway 192.168.1.1 that can be reached on device eth0.
ip route add default via 192.168.1.1 dev eth0

# Add an ipv4 route with mpls encapsulation attributes attached to it.
ip route add 10.1.1.0/30 encap mpls 200/300 via 10.1.1.1 dev eth0

# Add an IPv6 route with SRv6 encapsulation and two segments attached.
ip -6 route add 2001:db8:1::/64 encap seg6 mode encap segs 2001:db8:42::1,2001:db8:ffff::2 dev eth0

# Add an IPv6 route with SRv6 decapsulation and forward with lookup in VRF table.
ip -6 route add 2001:db8:1::/64 encap seg6local action End.DT46 vrftable 100 dev vrf100

# Add an IPv6 route with SRv6 End behavior with psp flavor enabled.
ip -6 route add 2001:db8:1::/64 encap seg6local action End flavors psp dev eth0

# Add an IPv6 route with SRv6 End behavior with next-csid flavor enabled.
ip -6 route add 2001:db8:1::/64 encap seg6local action End flavors next-csid dev eth0

# Add an IPv6 route with SRv6 End behavior with next-csid flavor enabled and user-provided Locator-Block and Locator-Node Function lengths.
ip -6 route add 2001:db8:1::/64 encap seg6local action End flavors next-csid lblen 48 nflen 16 dev eth0

# Add an IPv6 route with an IOAM Pre-allocated Trace encapsulation (ip6ip6) that only includes the hop limit and the node id, configured for the IOAM namespace 1 and a pre-allocated data block of 12 octets (will be injected in 2 packets every 5 packets).
ip -6 route add 2001:db8:1::/64 encap ioam6 freq 2/5 mode encap tundst 2001:db8:42::1 trace prealloc type 0x800000 ns 1 size 12 dev eth0

# Add an ipv4 route using nexthop object with id 10.
ip route add 10.1.1.0/30 nhid 10
```
