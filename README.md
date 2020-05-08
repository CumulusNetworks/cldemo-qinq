# DEPRECATED
## This repo is no longer maintained.<br>For a list of current demos, please visit:<br>https://gitlab.com/cumulus-consulting/goldenturtle/<br><br><br>

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
22:16:33.549411 44:38:39:00:00:50 > 44:38:39:00:00:4f, ethertype IPv4 (0x0800), length 152: 10.255.255.1.40079 > 10.255.255.3.4789: VXLAN, flags [I] (0x08), vni 15
00:03:00:11:11:01 > 00:03:00:33:33:01, ethertype 802.1Q (0x8100), length 102: vlan 10, p 0, ethertype IPv4, 10.0.100.11 > 10.0.100.13: ICMP echo request, id 5923, seq 1, length 64

22:16:33.549791 44:38:39:00:00:4f > 44:38:39:00:00:50, ethertype IPv4 (0x0800), length 152: 10.255.255.3.56123 > 10.255.255.1.4789: VXLAN, flags [I] (0x08), vni 15
00:03:00:33:33:01 > 00:03:00:11:11:01, ethertype 802.1Q (0x8100), length 102: vlan 10, p 0, ethertype IPv4, 10.0.100.13 > 10.0.100.11: ICMP echo reply, id 5923, seq 1, length 64

```

The following capture is done on the trunk facing interface on leaf03:
```
22:14:41.841168 00:03:00:11:11:01 > 00:03:00:33:33:01, ethertype 802.1Q-QinQ (0x88a8), length 106: vlan 100, p 0, ethertype 802.1Q, vlan 10, p 0, ethertype IPv4, 10.0.100.11 > 10.0.100.13: ICMP echo request, id 5871, seq 1, length 64

22:14:41.841494 00:03:00:33:33:01 > 00:03:00:11:11:01, ethertype 802.1Q-QinQ (0x88a8), length 106: vlan 100, p 0, ethertype 802.1Q, vlan 10, p 0, ethertype IPv4, 10.0.100.13 > 10.0.100.11: ICMP echo reply, id 5871, seq 1, length 64
```

The following capture is done on the fabric facing interface on leaf01:
```
22:15:17.790424 44:38:39:00:00:53 > 44:38:39:00:00:54, ethertype IPv4 (0x0800), length 152: 10.255.255.1.40079 > 10.255.255.3.4789: VXLAN, flags [I] (0x08), vni 15
00:03:00:11:11:01 > 00:03:00:33:33:01, ethertype 802.1Q (0x8100), length 102: vlan 10, p 0, ethertype IPv4, 10.0.100.11 > 10.0.100.13: ICMP echo request, id 5908, seq 4, length 64
22:15:17.791550 44:38:39:00:00:54 > 44:38:39:00:00:53, ethertype IPv4 (0x0800), length 152: 10.255.255.3.56123 > 10.255.255.1.4789: VXLAN, flags [I] (0x08), vni 15
00:03:00:33:33:01 > 00:03:00:11:11:01, ethertype 802.1Q (0x8100), length 102: vlan 10, p 0, ethertype IPv4, 10.0.100.13 > 10.0.100.11: ICMP echo reply, id 5908, seq 4, length 64
```

The following capture is done on the access port facing interface on leaf01:
```
22:14:41.840439 00:03:00:11:11:01 > 00:03:00:33:33:01, ethertype 802.1Q (0x8100), length 102: vlan 10, p 0, ethertype IPv4, 10.0.100.11 > 10.0.100.13: ICMP echo request, id 5871, seq 1, length 64

22:14:41.842033 00:03:00:33:33:01 > 00:03:00:11:11:01, ethertype 802.1Q (0x8100), length 102: vlan 10, p 0, ethertype IPv4, 10.0.100.13 > 10.0.100.11: ICMP echo reply, id 5871, seq 1, length 64
```