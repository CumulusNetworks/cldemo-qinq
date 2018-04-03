# cldemo-qinq

This demo is mean to create a base line configuration for QinQ example.

Currently this demo only includes flat files can be copied and pasted into cldemo-vagrant. There are plans in the future to add automation to the repo to automatically configure the infrastructure.

This demo aligns with the documentation: https://docs.cumulusnetworks.com/display/DOCS/Hybrid+Cloud+Connectivity+with+QinQ+and+VXLANs

# Steps to run demo

```
git clone https://github.com/CumulusNetworks/cldemo-vagrant.git
cd cldemo-vagrant
vagrant up oob-mgmt-server oob-mgmt-switch
vagrant up leaf01 leaf02 leaf03 leaf04 spine01 spine02
```

```
vagrant ssh oob-mgmt-server
git clone https://github.com/CumulusNetworks/cldemo-qinq.git
cd cldemo-qinq
cd NCLU
```

Inside the NCLU folder is the configurations. After pasting in the configurations, the environment should be set up to pass traffic.

# Verification

The following capture is done on the fabric facing interface on leaf03:
```
cumulus@leaf03:~$ sudo tcpdump -lne -i swp51
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on swp51, link-type EN10MB (Ethernet), capture size 262144 bytes
21:38:09.714726 44:38:39:00:00:4f > 44:38:39:00:00:50, ethertype IPv4 (0x0800), length 148: 10.255.255.3.40772 > 10.255.255.1.4789: VXLAN, flags [I] (0x08), vni 15
00:03:00:33:33:01 > 00:03:00:11:11:01, ethertype IPv4 (0x0800), length 98: 10.0.100.13 > 10.0.100.11: ICMP echo request, id 6457, seq 674, length 64

21:38:09.716209 44:38:39:00:00:50 > 44:38:39:00:00:4f, ethertype IPv4 (0x0800), length 148: 10.255.255.1.48301 > 10.255.255.3.4789: VXLAN, flags [I] (0x08), vni 15
00:03:00:11:11:01 > 00:03:00:33:33:01, ethertype IPv4 (0x0800), length 98: 10.0.100.11 > 10.0.100.13: ICMP echo reply, id 6457, seq 674, length 64
```

The following capture is done on the trunk facing interface on leaf03:
```
21:39:09.803229 00:03:00:33:33:01 > 00:03:00:11:11:01, ethertype 802.1Q-QinQ (0x88a8), length 102: vlan 100, p 0, ethertype IPv4, 10.0.100.13 > 10.0.100.11: ICMP echo request, id 6457, seq 734, length 64

21:39:09.807558 00:03:00:11:11:01 > 00:03:00:33:33:01, ethertype 802.1Q-QinQ (0x88a8), length 102: vlan 100, p 0, ethertype IPv4, 10.0.100.11 > 10.0.100.13: ICMP echo reply, id 6457, seq 734, length 64
```

The following capture is done on the fabric facing interface on leaf01:
```
21:40:11.893548 44:38:39:00:00:54 > 44:38:39:00:00:53, ethertype IPv4 (0x0800), length 148: 10.255.255.3.40772 > 10.255.255.1.4789: VXLAN, flags [I] (0x08), vni 15
00:03:00:33:33:01 > 00:03:00:11:11:01, ethertype IPv4 (0x0800), length 98: 10.0.100.13 > 10.0.100.11: ICMP echo request, id 6457, seq 796, length 64

21:40:11.894194 44:38:39:00:00:53 > 44:38:39:00:00:54, ethertype IPv4 (0x0800), length 148: 10.255.255.1.48301 > 10.255.255.3.4789: VXLAN, flags [I] (0x08), vni 15
00:03:00:11:11:01 > 00:03:00:33:33:01, ethertype IPv4 (0x0800), length 98: 10.0.100.11 > 10.0.100.13: ICMP echo reply, id 6457, seq 796, length 64
```

The following capture is done on the access port facing interface on leaf01:
```
21:43:06.147807 00:03:00:33:33:01 > 00:03:00:11:11:01, ethertype 802.1Q-QinQ (0x88a8), length 106: vlan 100, p 0, ethertype 802.1Q, vlan 10, p 0, ethertype IPv4, 10.0.100.13 > 10.0.100.11: ICMP echo request, id 6457, seq 970, length 64

21:43:06.148303 00:03:00:11:11:01 > 00:03:00:33:33:01, ethertype 802.1Q-QinQ (0x88a8), length 106: vlan 100, p 0, ethertype 802.1Q, vlan 10, p 0, ethertype IPv4, 10.0.100.11 > 10.0.100.13: ICMP echo reply, id 6457, seq 970, length 64
```