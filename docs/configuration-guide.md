# Configuration Guide

This guide records the main configuration used in the lab. Interface names may need changing if different Packet Tracer devices or ports are used.

## 1. SW1 configuration

### Create the VLANs

```cisco
enable
configure terminal
hostname SW1

vlan 10
 name EMPLOYEE
exit

vlan 20
 name SERVER
exit

vlan 99
 name ADMIN
exit
```

### Configure the access ports

```cisco
interface range fastethernet0/1-2
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast
exit

interface fastethernet0/3
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast
exit

interface fastethernet0/4
 switchport mode access
 switchport access vlan 99
 spanning-tree portfast
exit
```

### Configure the trunk

```cisco
interface gigabitethernet0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20,99
 no shutdown
exit
```

### Configure switch management

```cisco
interface vlan 99
 ip address 192.168.99.2 255.255.255.0
 no shutdown
exit

ip default-gateway 192.168.99.1
end
write memory
```

## 2. EDGE-R1 configuration

### Configure router-on-a-stick

```cisco
enable
configure terminal
hostname EDGE-R1

interface gigabitethernet0/0
 no ip address
 no shutdown
exit

interface gigabitethernet0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0
exit

interface gigabitethernet0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0
exit

interface gigabitethernet0/0.99
 encapsulation dot1Q 99
 ip address 192.168.99.1 255.255.255.0
exit
```

### Configure DHCP

```cisco
ip dhcp excluded-address 192.168.10.1 192.168.10.20
ip dhcp excluded-address 192.168.99.1 192.168.99.20

ip dhcp pool EMPLOYEE
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
exit

ip dhcp pool ADMIN
 network 192.168.99.0 255.255.255.0
 default-router 192.168.99.1
exit
```

I did not create a DHCP pool for VLAN 20 because the internal server uses a static address.

### Configure the outside link and default route

```cisco
interface gigabitethernet0/1
 ip address 203.0.113.2 255.255.255.252
 no shutdown
exit

ip route 0.0.0.0 0.0.0.0 203.0.113.1
```

### Configure PAT

```cisco
interface gigabitethernet0/0.10
 ip nat inside
exit

interface gigabitethernet0/0.20
 ip nat inside
exit

interface gigabitethernet0/0.99
 ip nat inside
exit

interface gigabitethernet0/1
 ip nat outside
exit

access-list 1 permit 192.168.10.0 0.0.0.255
access-list 1 permit 192.168.20.0 0.0.0.255
access-list 1 permit 192.168.99.0 0.0.0.255

ip nat inside source list 1 interface gigabitethernet0/1 overload
end
write memory
```

ACL 1 is being used to select addresses for NAT. It is not an ACL that blocks traffic between the VLANs.

## 3. ISP-R1 configuration

```cisco
enable
configure terminal
hostname ISP-R1

interface gigabitethernet0/0
 ip address 203.0.113.1 255.255.255.252
 no shutdown
exit

interface gigabitethernet0/1
 ip address 198.51.100.1 255.255.255.0
 no shutdown
exit

end
write memory
```

ISP-R1 does not need a route to the private VLANs for this test. Replies are sent to the translated source address `203.0.113.2`, which is on ISP-R1's directly connected `/30` network.

## 4. End-device addressing

### Employee PCs

Open **Desktop > IP Configuration** and select **DHCP**. Expected results start at:

```text
IP address:      192.168.10.21
Subnet mask:     255.255.255.0
Default gateway: 192.168.10.1
```

### Admin PC

Select **DHCP**. The expected first address is `192.168.99.21`, with gateway `192.168.99.1`.

### Internal Server

```text
IP address:      192.168.20.10
Subnet mask:     255.255.255.0
Default gateway: 192.168.20.1
```

### External Server

```text
IP address:      198.51.100.10
Subnet mask:     255.255.255.0
Default gateway: 198.51.100.1
```

## 5. Save the project

Save the running configurations on all Cisco devices with:

```cisco
write memory
```

Then save the Packet Tracer project as a `.pkt` file.

