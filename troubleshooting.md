This lab did not work on the first try. Here are the real problems encountered and how they were resolved:
1. ASA routes had swapped interfaces
Static routes on the ASA had the interface keyword backwards — traffic destined for the internet was being sent toward the inside interface and vice versa. Fixed by understanding that the interface in a route is determined by where the next-hop IP physically lives, not the destination.
2. OSPF adjacencies not forming after reload
Packet Tracer bug — OSPF config was intact but adjacencies would not reform after reopening the file. Fixed by removing and re-adding the OSPF process on each device. Root cause: PT does not always reinitialize routing protocols correctly on load.
3. BGP default route not installing in routing table
ISP was advertising a default route via BGP, Router1 showed it as gateway of last resort but it was not appearing as a routing table entry. This prevented default-information originate from working. Fixed by adding a static default route as a workaround for the PT bug.
4. NAT breaking DHCP after adding centralized server
After enabling DHCP on Server1, PCs in the branch were sending requests that reached Router1 but got forwarded to ISP instead of Server1. Root cause: 192.168.100.0/24 was not advertised into OSPF so Router1 had no route to Server1 and used the default route toward ISP. Fixed by adding the server subnet to OSPF on MLS1.
5. DHCP Option 82 dropping requests
Enabling DHCP snooping caused DHCP requests to be silently dropped. Root cause: DHCP snooping inserts Option 82 into relayed packets which Server1 rejected. Fixed with no ip dhcp snooping information option on all switches.
6. ACL blocking traffic with wrong mask on ASA
ASA access-list was configured with a subnet mask instead of a wildcard mask causing unexpected traffic to be blocked. Identified by reading the simulation output which showed packets being dropped at the ACL. Fixed by correcting the mask format.
7. ISP missing return route for internal subnets
Ping from ASA to ISP was failing despite correct routing on ASA and Router1. ISP had no route back to 10.0.0.0/30 so reply packets were dropped. Fixed by advertising the missing subnet into BGP from Router1.
