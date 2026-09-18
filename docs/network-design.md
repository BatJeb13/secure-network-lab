# Network Design

## Aim

I wanted to build a small network that was more realistic than placing every device in one subnet. I separated the main device types, gave each group its own IP network and then added the services needed for communication inside and outside the lab.

## Logical design

| VLAN | Name | Subnet | Gateway | Addressing method |
|---:|---|---|---|---|
| 10 | EMPLOYEE | `192.168.10.0/24` | `192.168.10.1` | DHCP |
| 20 | SERVER | `192.168.20.0/24` | `192.168.20.1` | Static |
| 99 | ADMIN | `192.168.99.0/24` | `192.168.99.1` | DHCP, with a static switch management address |

I used `/24` subnets to keep the addressing clear. They are larger than this lab needs, but they make it easy to identify which VLAN an address belongs to while I am testing and troubleshooting.

## Physical connections

| From | Interface | To | Interface | Link purpose |
|---|---|---|---|---|
| Employee PC1 | F0 | SW1 | F0/1 | VLAN 10 access link |
| Employee PC2 | F0 | SW1 | F0/2 | VLAN 10 access link |
| Internal Server | F0 | SW1 | F0/3 | VLAN 20 access link |
| Admin PC | F0 | SW1 | F0/4 | VLAN 99 access link |
| SW1 | G0/1 | EDGE-R1 | G0/0 | 802.1Q trunk |
| EDGE-R1 | G0/1 | ISP-R1 | G0/0 | `203.0.113.0/30` WAN link |
| ISP-R1 | G0/1 | External Server | F0 | External test network |

## Why I used VLANs

Each VLAN is a separate Layer 2 broadcast domain. Employee broadcasts therefore stay inside VLAN 10 instead of reaching the server and administration devices. This also gives me clear points on EDGE-R1 where I can apply ACLs later.

The ports connected to end devices are access ports because they carry one VLAN. The link from SW1 to EDGE-R1 is a trunk because it needs to carry traffic for VLANs 10, 20 and 99 over one physical connection.

## Why I used router-on-a-stick

The 2960 switch is operating at Layer 2, so I used EDGE-R1 to route between the VLANs. G0/0 has one subinterface per VLAN:

| Subinterface | 802.1Q VLAN | IP address |
|---|---:|---:|
| G0/0.10 | 10 | `192.168.10.1/24` |
| G0/0.20 | 20 | `192.168.20.1/24` |
| G0/0.99 | 99 | `192.168.99.1/24` |

These addresses are also the default gateways for their subnets.

## Address allocation

EDGE-R1 is the DHCP server for VLANs 10 and 99. I excluded `.1` through `.20` from each pool so the lower part of each subnet is available for gateways, management addresses and other infrastructure.

The internal server uses the static address `192.168.20.10`. A server should have a predictable address so clients and network rules do not depend on an address that could change after a DHCP lease expires.

## External routing and PAT

EDGE-R1 has a default route to `203.0.113.1`, which is ISP-R1. Traffic for destinations that are not in one of the directly connected networks is sent to this next hop.

The internal `192.168.x.x` addresses are private RFC1918 addresses. I configured the three router subinterfaces as NAT inside interfaces and G0/1 as the NAT outside interface. An ACL selects all three internal subnets for translation.

NAT overload changes the source address to `203.0.113.2` when traffic leaves EDGE-R1. Multiple internal devices can share this address because the router tracks their separate sessions using transport-layer ports. ICMP traffic is tracked using ICMP identifiers instead of TCP or UDP ports.

The documentation addresses `198.51.100.0/24` and `203.0.113.0/30` are used only to simulate an outside network in the lab.

## Packet journey: employee PC to internal server

1. The employee PC sees that `192.168.20.10` is not in its local `/24` subnet.
2. It sends the packet to its default gateway, `192.168.10.1`.
3. SW1 forwards the frame on the trunk with an 802.1Q VLAN 10 tag.
4. EDGE-R1 receives it on G0/0.10 and checks its routing table.
5. The server network is directly connected through G0/0.20.
6. EDGE-R1 sends the traffic back over the trunk tagged for VLAN 20.
7. SW1 removes the tag when it forwards the frame through the server access port.

NAT is not used for this journey because both networks are on the inside of EDGE-R1.

## Packet journey: employee PC to external server

1. The PC sends the packet to `192.168.10.1` because the destination is outside its subnet.
2. EDGE-R1 matches the source address against the NAT ACL.
3. PAT translates the private source address to `203.0.113.2` and creates a translation entry.
4. The default route sends the packet to ISP-R1 at `203.0.113.1`.
5. ISP-R1 forwards it to the external network.
6. When the reply returns, EDGE-R1 uses its translation table to send it back to the original internal PC.

