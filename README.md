# Advanced-enterprise-topology
advanced Enterprise Network Lab — Cisco Packet Tracer
A full enterprise network simulation built from scratch in Cisco Packet Tracer, covering routing, security, redundancy, and network services across two sites connected via IPsec VPN.

Topology Overview
Main Site

Router1 — Edge router, BGP peering with ISP, OSPF redistribution
ASA0 — Firewall, NAT, ACLs, DMZ
MLS1 — Layer 3 core switch, inter-VLAN routing, DHCP relay
MLS2 — Distribution switch, port-channel uplink to MLS1
Switch1/2/3 — Access layer switches
Server1 — Centralized DHCP, NTP, Syslog server
DMZ Server — Web server (HTTP/HTTPS)
WLC — Wireless LAN Controller

Branch Site

Router2 — Edge router, BGP peering with ISP, OSPF redistribution
BranchMLS1 — Layer 3 distribution switch
BRASwitch — Access layer switch
Branch PCs — VLAN 41 (Branch-HR) and VLAN 60 (Branch-IT)

ISP

Dual connections to both sites via 203.0.113.0/30 and 203.0.113.4/30


Technologies Implemented
Routing

OSPF — Internal routing across main site and branch site
eBGP — Between Router1/Router2 and ISP (AS 100 ↔ AS 1)
Route redistribution — OSPF into BGP for full reachability
Default route propagation — Via OSPF default-information originate

Security

Cisco ASA Firewall — Stateful inspection, security zones (inside/outside/DMZ)
NAT — Dynamic PAT for inside hosts toward internet
ACLs — Granular traffic control on all ASA interfaces
IPsec VPN — Site-to-site tunnel between Router1 and Router2
SSH + AAA — Secure management access on all devices with local authentication
Port Security — MAC address sticky on all access ports
DHCP Snooping — Prevents rogue DHCP servers on all switches
DAI (Dynamic ARP Inspection) — Prevents ARP spoofing
BPDU Guard — Protects access ports from rogue switches

High Availability

HSRP — Gateway redundancy on MLS1/MLS2 for all VLANs
Rapid PVST+ — Fast STP convergence on all switches
Port-Channel — Link aggregation between MLS1 and MLS2
Dual ISP — Both sites independently connected to ISP

Network Services

Centralized DHCP — Server1 serving all VLANs via ip helper-address
NTP — All devices synchronized to Server1
Syslog — Centralized logging to Server1
DMZ Web Server — HTTP/HTTPS accessible from outside

VLANs
VLANNameSubnet10HR192.168.10.0/2420IT192.168.20.0/2440Management192.168.40.0/2450WiFi192.168.50.0/2460Branch-IT192.168.60.0/2441Branch-HR192.168.41.0/24100Servers192.168.100.0/24

Network Design Decisions
Why OSPF internally instead of static routes?
Static routes don't scale. OSPF automatically propagates routes across all devices — add a new subnet and every device learns it instantly.
Why BGP with ISP instead of a default static route?
BGP is the standard for internet routing. It allows proper route advertisement, path selection, and demonstrates understanding of how the real internet works. Static defaults don't scale past one ISP.
Why centralize DHCP on Server1 instead of router DHCP?
In enterprise networks DHCP is always on a dedicated server. Using ip helper-address to relay requests across VLANs mirrors real production design.
Why DMZ?
The web server needs to be reachable from outside but must never have direct access to the internal LAN. The DMZ security zone on the ASA enforces this isolation with explicit ACL rules.
Why HSRP?
If MLS1 goes down, PCs would lose their default gateway. HSRP provides a virtual IP that automatically fails over to MLS2 — zero downtime for end users.


IP Addressing Summary
DeviceInterfaceIP AddressRouter1g0/0203.0.113.2/30Router1g0/110.0.0.1/30Router2g0/0203.0.113.6/30Router2g0/110.0.2.1/30ASA0g1/1 (outside)10.0.0.2/30ASA0g1/2 (inside)10.0.1.1/30ASA0g1/3 (DMZ)172.16.0.1/30MLS1g0/210.0.1.2/30BranchMLS1g0/110.0.2.2/30Server1fa0192.168.100.10/24DMZ Serverfa0172.16.0.2/30ISPg0/0203.0.113.1/30ISPg0/1203.0.113.5/30

How to Open

Download the .pkt file
Open with Cisco Packet Tracer 8.x or later
Wait for all devices to boot (~30 seconds)
If OSPF adjacencies are not forming, remove and re-add OSPF on Router1 and ASA0 (known PT bug)


What I Learned

How routing protocols interact in a real multi-site topology
Why NAT exemption is needed for VPN traffic
How ASA security levels and ACLs work together
The difference between control plane (routing) and data plane (forwarding) troubleshooting
How to read Packet Tracer simulation output to pinpoint exactly where packets are dropped
That troubleshooting is 90% reading what the network is telling you and 10% fixing it
