# Troubleshooting Notes

I used a bottom-up approach when something did not work: physical link, VLAN and trunk, IP configuration, routing, then NAT. This avoids changing several parts of the configuration at once.

## `show interfaces trunk` is blank

This command only lists operational trunks. I checked:

```cisco
show interfaces status
show interfaces gigabitethernet0/1 switchport
```

The switch port should have `Administrative Mode: trunk`, and the router interface on the other end must be enabled:

```cisco
interface gigabitethernet0/0
 no shutdown
```

I also confirmed that the cable connects SW1 G0/1 to EDGE-R1 G0/0 and that the link indicators are green.

## A PC does not receive a DHCP address

Checks on SW1:

```cisco
show vlan brief
show interfaces trunk
```

Checks on EDGE-R1:

```cisco
show ip interface brief
show ip dhcp pool
show ip dhcp binding
show running-config | section dhcp
```

The client access port must be in the correct VLAN, that VLAN must be carried over the trunk, and the matching router subinterface must be up. The DHCP pool's `network` and `default-router` values must also match the VLAN subnet.

The excluded range should be entered on one line:

```cisco
ip dhcp excluded-address 192.168.10.1 192.168.10.20
```

Entering `.1` and `.20` as separate commands excludes only those two individual addresses rather than the whole range.

## A PC can reach its gateway but not another VLAN

This shows that the local access VLAN and gateway are probably working. I then checked:

```cisco
show ip interface brief
show ip route
show running-config interface gigabitethernet0/0.10
show running-config interface gigabitethernet0/0.20
```

The subinterface must have the correct `encapsulation dot1Q` VLAN number and gateway address. The destination device also needs the correct default gateway so it can return traffic to the source network.

## The first ping fails but later pings work

This can be normal in Packet Tracer. Before sending the ICMP echo request, a device may need to use ARP to learn the destination or next-hop MAC address. I repeat the ping before treating this as a fault.

## Internal routing works but the external server does not respond

I checked the problem in this order:

1. Ping `203.0.113.1` from EDGE-R1.
2. Ping `198.51.100.10` from EDGE-R1.
3. Check the external server address, mask and gateway.
4. Check the default route on EDGE-R1.
5. Check NAT interface roles and the NAT ACL.

Commands used:

```cisco
show ip interface brief
show ip route
show access-lists
show ip nat statistics
show ip nat translations
```

The expected default route is:

```text
S* 0.0.0.0/0 via 203.0.113.1
```

## External ping works from EDGE-R1 but not from an internal PC

This points towards a NAT or internal gateway problem. I confirmed:

- G0/0.10, G0/0.20 and G0/0.99 are `ip nat inside`.
- G0/1 is `ip nat outside`.
- ACL 1 matches the source subnet.
- The PAT command uses ACL 1 and the G0/1 interface.

```cisco
show access-lists 1
show ip nat statistics
show running-config | include ip nat
```

After sending new traffic from a PC, I checked `show ip nat translations`. An empty table before traffic is generated is not automatically a fault because dynamic translations are created when they are needed and can time out.

## Useful fault-isolation order

1. Is the link up?
2. Is the access port in the correct VLAN?
3. Is the VLAN allowed and active on the trunk?
4. Does the host have the correct IP address, mask and gateway?
5. Are the router interfaces up with the correct addresses?
6. Does the routing table contain the expected connected and default routes?
7. Are NAT inside and outside applied in the right places?
8. Does the NAT ACL match the source network?

