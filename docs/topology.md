```mermaid
flowchart TD
    EXT["Internet or simulated external network"]
    FW["Edge firewall/router"]
    SW["Managed switch"]
    EMP["Employee device<br>VLAN 10"]
    SRV["Internal server<br>VLAN 20"]
    ADM["Administrator device<br>VLAN 99"]

    EXT --- FW
    FW ---|"802.1Q trunk"| SW
    SW ---|"Access port VLAN 10"| EMP
    SW ---|"Access port VLAN 20"| SRV
    SW ---|"Access port VLAN 99"| ADM
```

## Firewall

- This will be the central security device
  It should perform:
- Routing between VLANs
- Filtering between VLANs
- DHCP (if supported by choosen platform)
- NAT for internet access
- Logging of permitted and blocked traffic
- Firewall adim from VLAN 99

## Switch

This will provide:

- VLAN creation
- Access ports for end devices
- A trunk to the firewall
- A management address in VLAN 99
- Switch-port security and hardening

# VLANs

## VLAN 10: Employees

- This contains normal employee devices
- Employees will have the DHCP apply network settings
- They need internet access and limited access to approved internal services
- They shouldn't be able to mange or freely explore the server VLAN

### Example

- EMPLOYEE-PC-01
- Address asigned with DHCP
- Expected range: 192.168.10.100 - 192.168.10.199

## VLAN 20: Servers

- Servers normally use static addresses cause clients and firewalls need to know where to find them
- Initally one machine can provide both web service and logging service. (seperate later)

### Example

- Internal web server: 192.168.20.10
- Central syslog server: 192.168.20.20
  Employee devices might be allowed to access HTTPS on the web server but not SSH, file sharing, databse ports or other unnecessary services

## VLAN 99: Administration

- This is the most trusted network
  It contains:
- Admin workstation
- Firewall management interface
- Switch management interface
- Ant future monitoring or config systems
