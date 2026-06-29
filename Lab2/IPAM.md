**IP segments**
| Name                        | IPv4/mask            | IPv6/mask              | Purpose                    |
| --------------------------- | -------------------- | ---------------------- | -------------------------- |
| Edge-to-Core                | 203.0.113.0/31       | 2001:db8:0000::/127    | P2P connection             |
| Core-to-all                 | **198.51.100.0/24**  | **2001:db8:0001::/64** | **To all connection(summary)** |
| Core-to-PE-Kirovskiy        | 198.51.100.0/31      | 2001:db8:0001::/127    | P2P connection             |
| Core-to-PE-Volgskiy         | 198.51.100.2/31      | 2001:db8:0001::2/127   | P2P connection             |
| PE-Kirovskiy-to-PE-Volgskiy | 198.51.100.4/31      | 2001:db8:0001::4/127   | P2P connection             |
| PE-Kirovskiy-CPE            | **198.51.100.8/29**  | **not supported**      | **To all connection(summary)** |
| PE-Kirovskiy-to-CPE-5       | 198.51.100.8/30      | not supported          | legacy P2P connection      |
| PE-Kirovskiy-to-CPE-7       | 198.51.100.12/30     | not supported          | legacy P2P connection      |
| PE-Volgskiy-to-CPE          | **198.51.100.16/28** | **not supported**      | **To all connection(summary)** |
| PE-Volgskiy-to-CPE-4        | 198.51.100.16/30     | not supported          | legacy P2P connection      |
| PE-Volgskiy-to-CPE-6        | 198.51.100.20/30     | not supported          | legacy P2P connection      |
| Free                        | 198.51.100.32/27     | not supported          | Reserved to future use     |
| Free                        | 198.51.100.64/26     | not supported          | Reserved to future use     |
| Free                        | 198.51.100.128/26    | not supported          | Reserved to future use     |
| Free                        | 198.51.100.192/27    | not supported          | Reserved to future use     |
| Free                        | 198.51.100.224/28    | not supported          | Reserved to future use     |
| Free                        | 198.51.100.240/29    | not supported          | Reserved to future use     |
| Free                        | 198.51.100.248/30    | not supported          | Reserved to future use     |
| CPE-5 local network         | 10.0.0.0/24          | not supported          | local network              |
| CPE-7 local network         | 10.0.0.0/24          | not supported          | local network              |
| CPE-4 local network         | 10.0.1.0/24          | not supported          | local network              |
| CPE-6 local network         | 10.0.1.0/24          | not supported          | local network              |
| Loopback                    | 192.0.2.128/25       | not supported          | Loopback IP(BGP peering)   |

**IPv4/IPv6 address for routers**
| Hostname     | Interface | To              | IPv4 address     | IPv6 address         |
| ------------ | --------- | --------------- | ---------------- | -------------------- |
| EDGE-SARATOV | e1/0/1    | CORE-SARATOV    | 203.0.113.1/31   | 2001:db8:0000::1/127 |
| CORE-SARATOV | e4        | TO EDGE-SARATOV | 203.0.113.2/31   | 2001:db8:0000::2/127 |
| CORE-SARATOV | e1        | PE-KIROVSKIY    | 198.51.100.0/31  | 2001:db8:0001::0/127 |
| CORE-SARATOV | e2        | PE-VOLGSKIY     | 198.51.100.2/31  | 2001:db8:0001::2/127 |
| PE-KIROVSKIY | e1/0/0    | CORE-SARATOV    | 198.51.100.1/31  | 2001:db8:0001::0/127 |
| PE-VOLGSKIY  | e1/0/3    | CORE-SARATOV    | 198.51.100.3/31  | 2001:db8:0001::0/127 |
| PE-KIROVSKIY | e1/0/1    | PE-VOLGSKIY     | 198.51.100.4/31  | 2001:db8:0001::4/127 |
| PE-VOLGSKIY  | e1/0/2    | PE-KIROVSKIY    | 198.51.100.5/31  | 2001:db8:0001::5/127 |
| PE-KIROVSKIY | e1/0/3    | CPE-5           | 198.51.100.9/30  | not supported        |
| PE-KIROVSKIY | e1/0/2    | CPE-7           | 198.51.100.13/30 | not supported        |
| PE-VOLGSKIY  | e1/0/0    | CPE-4           | 198.51.100.17/30 | not supported        |
| PE-VOLGSKIY  | e1/0/1    | CPE-6           | 198.51.100.21/30 | not supported        |
| CPE-5        | eth1      | PE-KIROVSKIY    | 198.51.100.10/30 | not supported        |
| CPE-7        | eth1      | PE-KIROVSKIY    | 198.51.100.14/30 | not supported        |
| CPE-4        | eth1      | PE-VOLGSKIY     | 198.51.100.18/30 | not supported        |
| CPE-6        | eth1      | PE-VOLGSKIY     | 198.51.100.22/30 | not supported        |
| CPE-5-DG     | eth2      |                 | 10.0.0.254/24    | not supported        |
| CPE-7-DG     | eth2      |                 | 10.0.0.254/24    | not supported        |
| CPE-4-DG     | eth2      |                 | 10.0.1.254/24    | not supported        |
| CPE-6-DG     | eth2      |                 | 10.0.1.254/24    | not supported        |
| EDGE-SARATOV | loopback0 | internal        | 192.0.2.129/32    | not supported        |
| CORE-SARATOV | loopback0 | internal        | 192.0.2.130/32    | not supported        |
| PE-KIROVSKIY | loopback0 | internal        | 192.0.2.131/32    | not supported        |
| PE-VOLGSKIY  | loopback0 | internal        | 192.0.2.132/32    | not supported        |  

