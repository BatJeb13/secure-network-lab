# Secure Network Lab

This is a small enterprise-style network that I built in Cisco Packet Tracer to improve my practical understanding of VLANs, inter-VLAN routing, DHCP and NAT.

The network separates employee devices, internal servers and administration devices into three VLANs. A router-on-a-stick design provides routing between the VLANs, while PAT allows the private networks to reach a simulated external network through one outside address.

## What I configured

- Three VLANs for employee, server and administration traffic
- Access ports for end devices and an 802.1Q trunk to the router
- Router-on-a-stick inter-VLAN routing
- DHCP for employee and administration devices
- Static addressing for the internal server
- A simulated ISP and external test server
- A default route towards the ISP
- NAT overload, also known as PAT
- A management IP address for the switch
- A structured connectivity test plan

## Topology

```text
Employee PCs ----\
                  \
Internal Server ---- SW1 ===== EDGE-R1 ----- ISP-R1 ----- External Server
                  /       trunk       /30              198.51.100.10
Admin PC --------/          |
                            |-- VLAN 10: Employee
                            |-- VLAN 20: Server
                            `-- VLAN 99: Admin
```

`=====` represents the 802.1Q trunk between SW1 and EDGE-R1.

## Addressing summary

| Network or device | Address | Purpose |
|---|---:|---|
| Employee VLAN 10 | `192.168.10.0/24` | Employee devices |
| EDGE-R1 G0/0.10 | `192.168.10.1/24` | Employee default gateway |
| Employee PCs | DHCP from `.21` | Client addressing |
| Server VLAN 20 | `192.168.20.0/24` | Internal servers |
| EDGE-R1 G0/0.20 | `192.168.20.1/24` | Server default gateway |
| Internal Server | `192.168.20.10/24` | Static server address |
| Admin VLAN 99 | `192.168.99.0/24` | Administration devices |
| EDGE-R1 G0/0.99 | `192.168.99.1/24` | Admin default gateway |
| SW1 management SVI | `192.168.99.2/24` | Switch management |
| Admin PC | DHCP from `.21` | Client addressing |
| EDGE-R1 G0/1 | `203.0.113.2/30` | Outside/NAT interface |
| ISP-R1 G0/0 | `203.0.113.1/30` | ISP-facing next hop |
| ISP-R1 G0/1 | `198.51.100.1/24` | External network gateway |
| External Server | `198.51.100.10/24` | External connectivity test |

## Repository documentation

- [`docs/network-design.md`](docs/network-design.md) explains the design choices and packet flows.
- [`docs/configuration-guide.md`](docs/configuration-guide.md) contains the build steps and IOS commands.
- [`docs/test-plan.md`](docs/test-plan.md) contains the checks I used to verify the network.
- [`docs/troubleshooting.md`](docs/troubleshooting.md) records common faults and the commands I used to investigate them.
- [`docs/security-and-limitations.md`](docs/security-and-limitations.md) explains what the current lab does and does not secure.

## Main verification commands

```text
show vlan brief
show interfaces trunk
show ip interface brief
show ip route
show ip dhcp binding
show ip dhcp pool
show ip nat translations
show ip nat statistics
show running-config
```

## What I learned

The most useful part of this project was seeing how the different topics work together. VLANs create separate Layer 2 broadcast domains, the router subinterfaces provide Layer 3 gateways, DHCP supplies client addressing, the default route sends unknown traffic to the ISP, and PAT translates the private source addresses when traffic leaves the network.

## Current limitation

The VLANs are separated at Layer 2, but EDGE-R1 currently routes between them. This means VLANs provide segmentation but not a complete access-control policy by themselves. The next improvement is to add extended ACLs so that employee devices cannot access the Admin VLAN while authorised administration traffic is still allowed.

