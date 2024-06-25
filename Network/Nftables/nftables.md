# Nftables
Nftables (using the nft command) is similar to iptables but more efficient.
It is based on tables that contain chains that contain rules (like iptables)
Nftables can be used either via the nft command or directly by using .nft config files

List of table family types :
- ip : IPv4 address family.
- ip6 :IPv6 address family.
- inet :Internet (IPv4/IPv6) address family.
- arp : ARP address family, handling IPv4 ARP packets.
- bridge :Bridge address family, handling packets which traverse a bridge device.
- netdev : Netdev address family, handling packets on ingress and egress.

List of ipv4/ipv6/inet address families (they are the same for bridge) :
- prerouting : All packets entering the system are processed by the prerouting hook. It is invoked before the routing process and is used for early filtering or changing packet attributes that affect routing.
- input : Packets delivered to the local system are processed by the input hook.
- forward : Packets forwarded to a different host are processed by the forward hook.
- output : Packets sent by local processes are processed by the output hook.
- postrouting : All packets leaving the system are processed by the postrouting hook.
- ingress : All packets entering the system are processed by this hook. It is invoked before layer 3 protocol handlers, hence before the prerouting hook, and it can be used for filtering and policing. Ingress is only available for Inet family (since Linux kernel 5.10).

Arp address family : 
- input : Packets delivered to the local system are processed by the input hook.
- output : Packets send by the local system are processed by the output hook.

Netdev address family :
- ingress : All packets entering the system are processed by this hook. It is invoked after the network taps (ie. tcpdump), right after tc ingress and before layer 3 protocol handlers, it can be used for early filtering and policing.
- egress : All packets leaving the system are processed by this hook. It is invoked after layer 3 protocol handlers and before tc egress. It can be used for late filtering and policing.

## Nft commands
###  Tables
Add a table
```nft add table <table family type> <table name>```
<br>
Delete a specific table
```nft delete table <address family> <table name>```
<br>
List all tables for an address family
```nft list tables <address family>```
<br>
List all chains of a table
```nft list tabe=le <address family> <table name>```
<br>
List rules for a specific table :
```nft list ruleset <address family> <table name>```
<br>
Flush all rules for a specific table : 
``` nft flush ruleset <address family> <table name>```
<br>
Flush all chains and rules of a table
```nft flush table <address family> <table name>```
### Chains
Add a chain to a table
```nft add chain <address family> <table name> <chain name> { <chain attributes>}```
<br>
Delete a chain
```nft delete chain <address family> <table name> <chain name>```
<br>
Rename a chain
```nft rename chain <address family> <table name> <chain name> <new chain name>```
<br>
Flush all rules of a chain
```nft flush chain <address family> <table name> <chain name>```
<br>
List all chains of an address family
```nft list chains <address family>```
<br>
List all rules in a chain
```nft list chain <address family> <table name> <chain name>```
### Rules
Add a rule to a chain (append the rule to the chain)
```nft add rule <adress family> <table name> <chain name> <rule statements>```
<br>
Insert a rule (at the beginning of the chain)
```nft insert rule <adress family> <table name> <chain name> <rule statements>```
<br>
Replace a rule 
```nft replace rule <adress family> <table name> <rule number> <rule statements>```
<br>
Delete a rule
```nft delete rule <adress family> <table name> <rule number>```

## Editing /etc/nftables.conf
Basic conf file :
```bash
#!/usr/sbin/nft -f
flushruleset
table inet filter {
    chain input {
        type filter hook input priority 0;
    }
    chain forward {
        type filter hook input priority 0;
    }
    chain output {
        type filter hook input priority 0;
    }
}
```
Every new rule will be added in either of the chains (be careful with the indentation)

## Rules statements
Rules will be evaluating packets based on matches, which can be categorised as follow :
- ip
- ip6
- tcp
- udp
- udplite
- sctp
- dccp
- ah
- esp
- comp
- icmp
- icmpv6
- ether
- dst 
- frag
- hbh
- mh
- rt
- vlan
- arp
- ct
- meta
<br>
If a packet matches a rule an action determines what to do : 
- accept: Accept the packet and stop the remain rules evaluation.
- drop: Drop the packet and stop the remain rules evaluation.
- queue: Queue the packet to userspace and stop the remain rules evaluation.
- continue: Continue the ruleset evaluation with the next rule.
- return: Return from the current chain and continue at the next rule of the last chain. In a base chain it is equivalent to accept
- jump <chain>: Continue at the first rule of <chain>. It will continue at the next rule after a return statement is issued
- goto <chain>: Similar to jump, but after the new chain the evaluation will continue at the last chain instead of the one containing the goto statement
<br>
Interesting and useful matches :
ip : 
- protocol : upper layer protocol (tcp, icmp, udp...) ; e.g. ip protocol tcp, ip protocol != udp; ip protocol {tcp, udp}
- saddr : source address, can be a single address or a range or a subnet ; e.g. ip saddr 10.1.1.1 ; ip saddr 10.1.1.0/16 ; ip saddr 10.1.1.0-10.1.1.255
- daddr : destination address; can be a single address or a range ; e.g. ip daddr 10.1.1.1 ; ip daddr 10.1.1.0-10.1.1.255 ; ip daddr {10.0.0.1, 10.1.0.1}
ip6 : protocol, saddr, daddr
tcp : 
- dport : destination port, can be either numerical or a service ; e.g. tcp dport 22 ; tcp dport https
- sport : source port, can be either numerical or a service ; e.g. tcp sport 22 ; tcp sport https
udp : 
- dport
- sport
udplite :
- dport
- sport
sctp :
- dport
- sport
icmp
- type : icmp message type ; e.g. icmp type echo-reply ; icmp type {echo-request,echo-reply}
icmpv6 :
- type
ct : 
- state : state of the connection ; e.g. ct state established ; ct state {new, related}
Meta :
- iifname : input interface name ; e.g. meta iifname eth0 ; meta iifname {eth0, eth1}
- oifname : output ionterface name ; e.g. meta oifname eth0
ether :
- saddr : source mac address
To log any rule that mateches a packet,  add log or log prefix "<some information realted to the log>"
<br>
To reject a packet with a specific message type :
- reject with an icmp or icmpv6 message type
- reject with tcp reset
The syntax is : reject with <reject message type>
<br>
e.g. reject with icmp host-unreachable
<br>
Some example of rules 
<br>
To allow input from loopback 
```iifname lo accept;```
<br> 
To allow input connections that are in a certain state (e.g. established, new)
```ct state established,new accept;```
<br>
To drop invalid incoming conections
```ct state invalid drop;```
<br>
To limit or block icmp message types
```bash
ip6 nexthdr icmpv6 icmpv6 type <icmp message type> limit rate <number>/second <action>;
ip protocol icmp icmp type <icmp message type> limit rate <number>/second <action>;
``` 
To allow a connection from a specific port/service 
``` <protocol> dport <port or service> ip saddr <ip or subnet> <actions>;```
To enable a default policy (that will have the last word if no rules match a packet)
policy <drop reject accept>;```
