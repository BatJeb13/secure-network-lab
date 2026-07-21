# SW1 Configuration

## Purpose

SW1 connects the organisation's employee, server and administration devices. VLANs separate these device groups into different Layer 2 broadcast domains.

## VLAN and Port Assignments

| Port  | Connected Device | Mode   |       VLAN |
| ----- | ---------------- | ------ | ---------: |
| Fa0/1 | EMP-PC1          | Access |         10 |
| Fa0/2 | SRV1             | Access |         20 |
| Fa0/3 | ADMIN-PC1        | Access |         99 |
| G0/1  | EDGE-R1          | Trunk  | 10, 20, 99 |

## Configuration

```text
enable
configure terminal

vlan 10
 name EMPLOYEES
 exit

vlan 20
 name SERVERS
 exit

vlan 99
 name ADMINISTRATION
 exit

VLAN10
interface fastEthernet0/1
 description EMP-PC1_VLAN10
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast
 no shutdown
 exit

VLAN20
interface fastEthernet0/2
 description SRV1_VLAN20
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast
 no shutdown
 exit

VLAN99
interface fastEthernet0/3
 description ADMIN-PC1_VLAN99
 switchport mode access
 switchport access vlan 99
 spanning-tree portfast
 no shutdown
 exit

interface gigabitEthernet0/1
 description TRUNK_TO_EDGE-R1
 switchport mode trunk
 switchport trunk allowed vlan 10,20,99
 no shutdown
 exit

end
copy running-config startup-config
```

## Verification Commands

```text
show vlan brief
show interfaces trunk
show interfaces fa0/1 switchport
show interfaces fa0/2 switchport
show interfaces fa0/3 switchport
show interfaces status
```
