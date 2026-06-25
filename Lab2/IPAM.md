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
