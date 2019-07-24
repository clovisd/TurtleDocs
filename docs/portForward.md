---
id: portForward
title: Port Forwarding for linux server s11.cl6.us
---
This document describes how to setup portforwarding for the linux server,
through a vpn tunnel

To setup ipforwarding using iptables is pretty simple. A quick background on iptables: There are two important parts of iptables for our usecase; the firewall and the NAT (network address translation). We're adding two rules for each port we want to forward; one rule to allow access through the firewall (even though we really don't need it), and one rule to the NAT table (this one is important). Essentially, we're asking iptables to take data coming from the external facing network interface (venet0 in our case) with a specific destination port, and change that ip to the ip of the vpn. 

The first rule, which is the firewall rule (not important) for port 80:
iptables -I FORWARD -i venet0 -p tcp -d 10.8.0.2 --dport 80 -j ACCEPT

-I is inserting the rule
-i indicated the incoming network interface
-p indicated the protocol
-d indicated the destination ip that we want to forward traffic to
--dport indicates the destination port, coming from the client
-j is the action the rule will take



The second rule, which is the NAT rule (important) for port 80:
iptables -t nat -I PREROUTING -i venet0 -p tcp --dport 80 -j DNAT --to-destination 10.8.0.2

-t indicates the table we want to add to (NAT)
-I is inserting the rule
-i indicated the incoming network interface
-p indicated the protocol
-d indicated the destination ip that we want to forward traffic to
--dport indicates the destination port, coming from the client
-j Is the action we want the rule to take (execute destinated NAT)
--to-destination indicates what address we want our NAT to give the packet


Here are all the rules we're using currently:
iptables -I FORWARD -i venet0 -p tcp -d 10.8.0.2 --dport 80 -j ACCEPT
iptables -t nat -I PREROUTING -i venet0 -p tcp --dport 80 -j DNAT --to-destination 10.8.0.2


iptables -I FORWARD -i venet0 -p tcp -d 10.8.0.2 --dport 25565 -j ACCEPT
iptables -t nat -I PREROUTING -i venet0 -p tcp --dport 25565 -j DNAT --to-destination 10.8.0.2
iptables -I FORWARD -i venet0 -p udp -d 10.8.0.2 --dport 25565 -j ACCEPT
iptables -t nat -I PREROUTING -i venet0 -p udp --dport 25565 -j DNAT --to-destination 10.8.0.2


iptables -I FORWARD -i venet0 -p tcp -d 10.8.0.2 --dport 8123 -j ACCEPT
iptables -t nat -I PREROUTING -i venet0 -p tcp --dport 8123 -j DNAT --to-destination 10.8.0.2


iptables -I FORWARD -i venet0 -p tcp -d 10.8.0.2 --dport 8080 -j ACCEPT
iptables -t nat -I PREROUTING -i venet0 -p tcp --dport 8080 -j DNAT --to-destination 10.8.0.2


iptables -I FORWARD -i venet0 -p tcp -d 10.8.0.2 --dport 8081 -j ACCEPT
iptables -t nat -I PREROUTING -i venet0 -p tcp --dport 8081 -j DNAT --to-destination 10.8.0.2

