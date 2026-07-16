# Network Test Plan

## Testing approach

- Positive tests confirm that approved services work. Negative tests confirm that prohibited connections are blocked.
- Evidence will be stored in the `screenshots/` directory.

## Test cases

| ID  | Requirement | Test                                                              | Expected result                                  | Actual result | Evidence | Status  |
| --- | ----------- | ----------------------------------------------------------------- | ------------------------------------------------ | ------------- | -------- | ------- |
| T01 | SR-01       | Connect the employee PC to VLAN 10 and request DHCP configuration | PC receives an address from `192.168.10.100–199` | Not run       | Pending  | Planned |
| T02 | SR-01       | Ping `192.168.10.1` from the employee PC                          | Ping succeeds                                    | Not run       | Pending  | Planned |
| T03 | SR-02       | Access the simulated external web service from the employee PC    | Connection succeeds                              | Not run       | Pending  | Planned |
| T04 | SR-03       | Open HTTPS on `192.168.20.10` from the employee PC                | Connection succeeds                              | Not run       | Pending  | Planned |
| T05 | SR-03       | Attempt SSH to `192.168.20.10` from the employee PC               | Connection is blocked                            | Not run       | Pending  | Planned |
| T06 | SR-04       | Attempt SSH to `192.168.99.2` from the employee PC                | Connection is blocked                            | Not run       | Pending  | Planned |
| T07 | SR-04       | Attempt to reach the administrator PC from the employee PC        | Connection is blocked                            | Not run       | Pending  | Planned |
| T08 | SR-06       | Use SSH from the administrator PC to the switch                   | Connection succeeds                              | Not run       | Pending  | Planned |
| T09 | SR-06       | Use SSH from the administrator PC to the firewall/router          | Connection succeeds                              | Not run       | Pending  | Planned |
| T10 | SR-05       | Initiate a connection from a server to the employee PC            | Connection is blocked                            | Not run       | Pending  | Planned |
| T11 | SR-09       | Generate traffic that matches no allow rule                       | Traffic is blocked and logged                    | Not run       | Pending  | Planned |
| T12 | SR-07       | Generate a firewall or switch event                               | Event appears on the syslog server               | Not run       | Pending  | Planned |
| T13 | SR-08       | Connect a device to an unused switch port                         | Device receives no connectivity                  | Not run       | Pending  | Planned |
| T14 | SR-10       | Attempt to manage a network device using Telnet                   | Connection is refused                            | Not run       | Pending  | Planned |
| T15 | SR-09       | Repeat approved tests after applying all firewall rules           | Approved services continue to work               | Not run       | Pending  | Planned |

## Evidence naming

Screenshots should use descriptive names such as:

- `T01-employee-dhcp.png`
- `T04-employee-https-success.png`
- `T05-employee-ssh-blocked.png`
- `T08-admin-switch-ssh.png`
- `T11-firewall-deny-log.png`
- `T12-syslog-received.png`

## Test status values

- Planned
- Pass
- Fail
- Blocked
- Retest required
