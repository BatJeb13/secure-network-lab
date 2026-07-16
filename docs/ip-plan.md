# Ip addressing plan

| Device or role              | VLAN | Address            | Assignment         |
| --------------------------- | ---- | ------------------ | ------------------ |
| Employee gateway            | 10   | 192.168.10/24      | Firewall interface |
| Employee devices            | 10   | 192.168.10.100–199 | DHCP               |
| Server gateway              | 20   | 192.168.20.1/24    | Firewall interface |
| Internal web server         | 20   | 192.168.20.10/24   | Static             |
| Syslog server               | 20   | 192.168.20.20/24   | Static             |
| Management gateway          | 99   | 192.168.99.1/24    | Firewall interface |
| Managed switch              | 99   | 192.168.99.2/24    | Static             |
| Administrator PC            | 99   | 192.168.99.10/24   | Static or reserved |
| External firewall interface | WAN  | Platform-dependent | Static or DHCP     |

Reserve lower addresses for infrastructure and higher ranges for DHCP clients.
