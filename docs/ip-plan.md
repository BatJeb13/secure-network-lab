# IP Addressing Plan

## Network Allocation

| Network        | VLAN | Purpose                               | Subnet          | Default Gateway |
| -------------- | ---: | ------------------------------------- | --------------- | --------------- |
| Employees      |   10 | Employee workstations                 | 192.168.10.0/24 | 192.168.10.1    |
| Servers        |   20 | Internal servers                      | 192.168.20.0/24 | 192.168.20.1    |
| Administration |   99 | Administration and network management | 192.168.99.0/24 | 192.168.99.1    |
| WAN            |  N/A | Edge router to simulated ISP          | 203.0.113.0/30  | N/A             |

The first usable address in each subnet is assigned to the firewall/router and acts as the default gateway.

## Device addresses

| Device    | Interface     | Address         | Allocation |
| --------- | ------------- | --------------- | ---------- |
| ISP-R1    | G0/0          | 203.0.113.1/30  | Static     |
| EDGE-R1   | G0/1          | 203.0.113.2/30  | Static     |
| EDGE-R1   | G0/0.10       | 192.168.10.1/24 | Static     |
| EDGE-R1   | G0/0.20       | 192.168.20.1/24 | Static     |
| EDGE-R1   | G0/0.99       | 192.168.99.1/24 | Static     |
| SW1       | VLAN 99       | 192.168.99.2/24 | Static     |
| EMP-PC1   | FastEthernet0 | DHCP            | Dynamic    |
| SRV1      | FastEthernet0 | 192.168.20.1/24 | Static     |
| ADMIN-PC1 | FastEthernet0 | 192.168.99.1/24 | Static     |

## DHCP Plan

Employee devices will receive addresses through DHCP.

- Network: `192.168.10.0/24`
- Default gateway: `192.168.10.1`
- DHCP range: `192.168.10.100–192.168.10.199`
- Reserved infrastructure range: `192.168.10.1–192.168.10.99`

Addresses below `.100` are reserved for infrastructure or future statically addressed devices.

## Addressing conventions

- `.1` is used for the default gateway.
- Low addresses are reserved for infrastructure.
- Servers receive static addresses.
- Employee devices receive DHCP addresses.
- Network and broadcast addresses are never assigned to devices.
- The VLAN 99 subnet is not used for ordinary employee devices.

Using `/24` networks makes the lab easier to understand and leaves room for additional simulated devices.
