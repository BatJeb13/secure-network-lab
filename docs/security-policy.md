# Security Policy

## Security requirements

| ID    | Requirement                                                                               |
| ----- | ----------------------------------------------------------------------------------------- |
| SR-01 | Employee devices must receive their network configuration through DHCP.                   |
| SR-02 | Employee devices must be able to access approved internet services.                       |
| SR-03 | Employee devices may access only HTTPS on the internal web server.                        |
| SR-04 | Employee devices must not access the management VLAN.                                     |
| SR-05 | Servers must not initiate unsolicited connections to employee devices.                    |
| SR-06 | Only the administrator workstation may manage the firewall and switch using SSH or HTTPS. |
| SR-07 | The firewall and switch must send logs to the central syslog server.                      |
| SR-08 | Unused switch ports must be administratively disabled.                                    |
| SR-09 | Traffic that is not explicitly permitted must be blocked.                                 |
| SR-10 | Insecure management services, including Telnet and unencrypted HTTP, must be disabled.    |

## Policy principles

- The network uses a default-deny policy. Traffic is blocked unless a documented rule permits it.
- Rules should be as specific as possible by identifying the source, destination and required service.
- A stateful firewall may automatically permit response traffic associated with an approved connection.

## Initial firewall-policy matrix

| ID    | Source                  | Destination                | Service                              | Action | Logging  |
| ----- | ----------------------- | -------------------------- | ------------------------------------ | ------ | -------- |
| FP-01 | Established connections | Original connection source | Established/related traffic          | Allow  | No       |
| FP-02 | Employee VLAN           | Firewall DHCP service      | DHCP, UDP 67–68                      | Allow  | No       |
| FP-03 | Employee VLAN           | Employee gateway           | ICMP                                 | Allow  | No       |
| FP-04 | Employee VLAN           | External network           | DNS, HTTP and HTTPS                  | Allow  | Optional |
| FP-05 | Employee VLAN           | `192.168.20.10`            | HTTPS, TCP 443                       | Allow  | Yes      |
| FP-06 | Employee VLAN           | Server VLAN                | All other traffic                    | Deny   | Yes      |
| FP-07 | Employee VLAN           | Management VLAN            | All traffic                          | Deny   | Yes      |
| FP-08 | Server VLAN             | External network           | DNS, NTP, HTTP and HTTPS for updates | Allow  | Optional |
| FP-09 | Server VLAN             | Employee VLAN              | New connections                      | Deny   | Yes      |
| FP-10 | Administrator PC        | Firewall and switch        | SSH and HTTPS                        | Allow  | Yes      |
| FP-11 | Administrator PC        | Internal servers           | Approved SSH and HTTPS management    | Allow  | Yes      |
| FP-12 | Firewall and switch     | `192.168.20.20`            | Syslog, UDP 514                      | Allow  | No       |
| FP-13 | Any other source        | Any other destination      | Any                                  | Deny   | Yes      |

The final syntax and exact rule order will depend on the chosen firewall or router platform.
