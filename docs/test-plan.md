# Test Plan

I tested the network in stages so that a fault could be narrowed down to one part of the design. The first ping can time out while Packet Tracer completes ARP, so I repeated it before recording a failure.

## Test results table

Replace the result column with the result from the final saved version of the lab.

| ID | Test | Source | Destination or command | Expected result | Actual result |
|---:|---|---|---|---|---|
| 1 | Check VLAN membership | SW1 | `show vlan brief` | F0/1-2 in VLAN 10, F0/3 in VLAN 20, F0/4 in VLAN 99 | To record |
| 2 | Check trunk | SW1 | `show interfaces trunk` | G0/1 trunking; VLANs 10,20,99 allowed and forwarding | To record |
| 3 | Check router interfaces | EDGE-R1 | `show ip interface brief` | Subinterfaces and outside interface are up/up | To record |
| 4 | Employee DHCP | Employee PC1 | `ipconfig` | Address in `192.168.10.0/24`, gateway `192.168.10.1` | To record |
| 5 | Admin DHCP | Admin PC | `ipconfig` | Address in `192.168.99.0/24`, gateway `192.168.99.1` | To record |
| 6 | Local gateway | Employee PC1 | `ping 192.168.10.1` | Replies received | To record |
| 7 | Inter-VLAN routing | Employee PC1 | `ping 192.168.20.10` | Replies received | To record |
| 8 | Switch management | Admin PC | `ping 192.168.99.2` | Replies received | To record |
| 9 | WAN next hop | EDGE-R1 | `ping 203.0.113.1` | Replies received | To record |
| 10 | External reachability | Employee PC1 | `ping 198.51.100.10` | Replies received | To record |
| 11 | DHCP binding | EDGE-R1 | `show ip dhcp binding` | Employee and Admin leases displayed | To record |
| 12 | PAT translation | EDGE-R1 | `show ip nat translations` after test 10 | Translation for the employee PC displayed | To record |
| 13 | NAT counters | EDGE-R1 | `show ip nat statistics` | Inside/outside interfaces correct and hit count increases | To record |
| 14 | Default route | EDGE-R1 | `show ip route` | `S* 0.0.0.0/0` via `203.0.113.1` | To record |

## Test procedure

### Layer 1 and Layer 2

1. Check that the required links are green in Packet Tracer.
2. Run `show interfaces status` on SW1.
3. Run `show vlan brief` and confirm the access ports.
4. Run `show interfaces trunk` and confirm G0/1 is operational as a trunk.

### DHCP and local connectivity

1. Select DHCP on Employee PC1, Employee PC2 and the Admin PC.
2. Run `ipconfig` on each PC.
3. Confirm the address, mask and gateway belong to the correct VLAN.
4. On EDGE-R1, run `show ip dhcp binding`.
5. Ping the local default gateway from each PC.

### Inter-VLAN routing

1. From an Employee PC, ping `192.168.20.10`.
2. From the Admin PC, ping `192.168.10.1` and `192.168.20.10`.
3. Check `show ip route` on EDGE-R1. All three private `/24` networks should appear as connected routes.

These tests prove routing works, but successful traffic between all VLANs also confirms that there is not yet an ACL-based security policy between them.

### External connectivity and PAT

1. From EDGE-R1, ping ISP-R1 at `203.0.113.1`.
2. From EDGE-R1, ping the external server at `198.51.100.10`.
3. From Employee PC1, ping `198.51.100.10`.
4. Immediately run `show ip nat translations` on EDGE-R1.
5. Run `show ip nat statistics` and confirm translations or hit counters are present.

## Evidence to capture

Useful screenshots for the repository are:

- The complete Packet Tracer topology
- `show vlan brief`
- `show interfaces trunk`
- `show ip interface brief` on EDGE-R1
- Successful employee-to-server ping
- Successful employee-to-external-server ping
- `show ip dhcp binding`
- `show ip nat translations`

Screenshots should avoid covering device names or IP labels so somebody reviewing the repository can understand what is being proved.

