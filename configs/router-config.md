# EDGE-R1 Router Configuration

## Purpose

EDGE-R1 provides router-on-a-stick inter-VLAN routing for the employee,
server and administration VLANs. Each VLAN has a router subinterface that
acts as its default gateway.

## Gateway Plan

| VLAN | Purpose        | Subnet          | Gateway      | Subinterface |
| ---: | -------------- | --------------- | ------------ | ------------ |
|   10 | Employees      | 192.168.10.0/24 | 192.168.10.1 | G0/0.10      |
|   20 | Servers        | 192.168.20.0/24 | 192.168.20.1 | G0/0.20      |
|   99 | Administration | 192.168.99.0/24 | 192.168.99.1 | G0/0.99      |

## Configuration

```text
enable
configure terminal

interface gigabitEthernet0/0
 description TRUNK_TO_SW1
 no ip address
 no shutdown
 exit

interface gigabitEthernet0/0.10
 description GATEWAY_VLAN10_EMPLOYEES
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0
 exit

interface gigabitEthernet0/0.20
 description GATEWAY_VLAN20_SERVERS
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0
 exit

interface gigabitEthernet0/0.99
 description GATEWAY_VLAN99_ADMIN
 encapsulation dot1Q 99
 ip address 192.168.99.1 255.255.255.0
 exit

end
copy running-config startup-config
```

## Host Addressing

| Device    | IP address     | Subnet mask   | Default gateway |
| --------- | -------------- | ------------- | --------------- |
| EMP-PC1   | 192.168.10.100 | 255.255.255.0 | 192.168.10.1    |
| SRV1      | 192.168.20.10  | 255.255.255.0 | 192.168.20.1    |
| ADMIN-PC1 | 192.168.99.10  | 255.255.255.0 | 192.168.99.1    |

## Verification Commands

```text
show ip interface brief
show ip route
show arp
show running-config interface gigabitEthernet0/0.10
show running-config interface gigabitEthernet0/0.20
show running-config interface gigabitEthernet0/0.99
```

## Test Results

| Test                     | Expected result     | Actual result |
| ------------------------ | ------------------- | ------------- |
| EMP-PC1 → 192.168.10.1   | Success             |               |
| SRV1 → 192.168.20.1      | Success             |               |
| ADMIN-PC1 → 192.168.99.1 | Success             |               |
| EMP-PC1 → SRV1           | Success before ACLs |               |
| ADMIN-PC1 → EMP-PC1      | Success before ACLs |               |

In the future the pings such as from EMP-PC1 to ADMIN-PC1 will be blocked but for now it has not been configured so works as expected
