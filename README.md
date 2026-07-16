# secure-network-lab

## Project objective

- The objective of this project is to design, implement, secure and test a small segmented organisation network
- The network seperates emplyee devices, internal servers and network management devices using VLANS
- Traffic between these networks will be controlled according to a default deny policy

The goal is to demonstrate practical knowledge of:

- VLANs
- IP addressing
- DHCP
- Routing
- Firewall rules
- NAT
- Secure administration
- Centralised logging
- Network testing

## Business scenario

- General Consulting is a small organisation with approximately 20 employees and one IT adimistrator
- Employees need internet access and access to an internal web application.
- The organisation also operates a central logging server
- Network equipment must be only managed by the IT admin

Placing all devices on one network would be insecure therefore the network is devided into seperate employee, server and admin VLANs

## Network topology

The network contains:

- Internet or simulated external network
- An edge firewall/router
- A managed switch
- Employee devices on VLAN 10
- Servers on VLAN 20
- Admin workstation on VLAN 99

(SEE docs/topology.md)

## IP addressing plan

The network will use a IPv4 with the following subnets:

| VLAN | Name       | Subnet            | Purpose                               |
| ---: | ---------- | ----------------- | ------------------------------------- |
|   10 | EMPLOYEES  | `192.168.10.0/24` | Employee workstations                 |
|   20 | SERVERS    | `192.168.20.0/24` | Internal services                     |
|   99 | MANAGEMENT | `192.168.99.0/24` | Administration and network management |

(SEE complete ip plan docs/ip-plan.md)

## Technologies used

Planned technologies:

- Cisco packet tracker
- Cisco IOS-style switch and router configuration
- VLANs and IEEE 802.1q trunking
- DHCP
- IPv4 routing
- Firewall rules or access-control lists
- NAT/PAT
- SSH
- Syslog
- Git and Github

## Security controls

Planeed controls:

- Seperation of employees, servers and administration using VLANs
- Default-deny filtering between networks
- Restricted employee access to server VLAN
- Management access restricted to VLAN 99
- SSH instead of Telnet
- Disabled unused switch ports
- Centralised network-device logging
- NAT for employee interent access

(SEE docs/security-policy.md)

## Configuration

- Sanitised devices will be added to the `configs/` directory in weeks 2 and 3
- Passwords, private keys and other secrets will not be comitted.

## Testing

- Both permitted and prohibitted traffic will be tested

(SEE docs/test-plan.md)

## Troubleshooting

Problems, causes and solutions discovered during implementation will be recorded here in weeks 2-4

## Limitations

- The initial implementation is a small educational lab rather than production network
- It uses a limited number of devices and simplified services.
- Possible future improvements include redundant network devices, a dedicated monitoring VLAN, VPN access, intrusion detection and more advanced switch-security controls.

## What I learned

This section will be completed after implementation and testing.
