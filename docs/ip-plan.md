# IP Addressing Plan

## VLAN networks

| VLAN | Name       | Network           | Subnet mask     | Default gateway | Assignment         |
| ---: | ---------- | ----------------- | --------------- | --------------- | ------------------ |
|   10 | EMPLOYEES  | `192.168.10.0/24` | `255.255.255.0` | `192.168.10.1`  | DHCP               |
|   20 | SERVERS    | `192.168.20.0/24` | `255.255.255.0` | `192.168.20.1`  | Static             |
|   99 | MANAGEMENT | `192.168.99.0/24` | `255.255.255.0` | `192.168.99.1`  | Static or reserved |

The first usable address in each subnet is assigned to the firewall/router and acts as the default gateway.

## Device addresses

| Device              | Interface or role    | VLAN | Address              | Assignment     |
| ------------------- | -------------------- | ---: | -------------------- | -------------- |
| Firewall/router     | Employee gateway     |   10 | `192.168.10.1/24`    | Static         |
| Employee PC         | Employee client      |   10 | `192.168.10.100–199` | DHCP           |
| Firewall/router     | Server gateway       |   20 | `192.168.20.1/24`    | Static         |
| Internal web server | HTTPS service        |   20 | `192.168.20.10/24`   | Static         |
| Syslog server       | Central logging      |   20 | `192.168.20.20/24`   | Static         |
| Firewall/router     | Management gateway   |   99 | `192.168.99.1/24`    | Static         |
| Managed switch      | Management interface |   99 | `192.168.99.2/24`    | Static         |
| Administrator PC    | Administration       |   99 | `192.168.99.10/24`   | Static         |
| Firewall/router     | WAN interface        |  WAN | Platform-dependent   | Static or DHCP |

## VLAN 10 DHCP scope

| Setting              | Value                                |
| -------------------- | ------------------------------------ |
| Network              | `192.168.10.0/24`                    |
| Address range        | `192.168.10.100–192.168.10.199`      |
| Default gateway      | `192.168.10.1`                       |
| DNS server           | To be selected during implementation |
| Suggested lease time | 24 hours                             |

Addresses below `.100` are reserved for infrastructure or future statically addressed devices.

## Addressing conventions

- `.1` is used for the default gateway.
- Low addresses are reserved for infrastructure.
- Servers receive static addresses.
- Employee devices receive DHCP addresses.
- Network and broadcast addresses are never assigned to devices.
- The VLAN 99 subnet is not used for ordinary employee devices.

Using `/24` networks makes the lab easier to understand and leaves room for additional simulated devices.
