---
title: Ideas - Commands
date: February 10, 2025
tags: []
---


# VI Commands 
# --------------------------------------------------------------------------------
## remove all the lines containing the pattern "mv"
 * :g/"pattern-to-delete"/g


# SHELL Commands 
# --------------------------------------------------------------------------------
## remove all the lines containing the pattern "mv"
 * sed '/mv/d' ./history.txt >& history1.txt

## delete columns from a file
 * sed 's/^ *//' history.txt | cut -d" " -f2- > history1.txt

# TCPdump Commands 
# --------------------------------------------------------------------------------
## capture packet on particular interface and copy them in a file
* tcpdump -nnvvXSs 1514 -u -vv -i bridge0  -w /tmp/tt.pcap
  ip.src==10.1.2.35 || ip.dst == 10.1.2.35 && http ( filters in wireshark )

## capture packet on particular interface if they are icmp or arp
* tcpdump -i qr-80875c88-72 -v "icmp or arp"

# ping Commands 
# --------------------------------------------------------------------------------
* ping -I 10.219.2.8 138.241.117.109 -s 800 -c 1000 -f
