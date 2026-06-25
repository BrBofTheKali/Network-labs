# 🌐 Work Performed Description

---

## 🎯 Task Statement

There is an operator backbone for communication between Saint Petersburg and Moscow. Each city has a core router (**core-router**).

It is necessary to provide:
- ✅ Routing between 2 clients from different cities, which have been allocated special addresses and AS numbers
- ✅ Internet access

For the work, it is necessary to raise **BGP** and **IS-IS** protocols, configure **IPv4** and **IPv6** addressing, and organize simple **route-maps** for route filtering.

 📌 For routing between routers, special addresses were used (`RFC 5737` & `RFC 3849` & `RFC 1918`), and a private range was allocated for ASN (`RFC 6996`).

---

## 🖥️ Used Router Images

| Role | Image | Version |
|:---:|:---|:---|
| 🟢 Edge | **EcoRouterOS** | `3.2.6.2` |
| 🔵 Core | **Cisco IOS XE** | `16.07.01` |
| 🟡 CPE (client) | **MikroTik RouterOS** | `7.8` |
| 🌐 Internet | **Cisco IOS** | `15.3` |

---

## 🗺️ Topology and Addressing

| Document | Description |
|:---|:---|
| 📊 [Topology](screenshots/Topology.png) | Network topology |
| 🌍 [IPv4](screenshots/IPv4.png) | IP segments for connectivity |
| 🌐 [IS-IS](screenshots/IS-IS.png) | Diagram with roles, areas, and NET addresses |
| 🔒 [IPv4Filter](screenshots/IPv4Filter.png) | Traffic filtering logic (IPv4) |
| 🔒 [IPv6Filter](screenshots/IPv6Filter.png) | Traffic filtering logic (IPv6) |
| 🌍 [IPv6](screenshots/IPv6.png) | IPv6 addressing |

---

## ⚙️ Configuration

### 1️⃣ Address Space

#### 🔗 Device Interconnect Links

| Hostnames link | IPv4 | IPv6 |
|:---|:---|:---|
| `Internet ↔ Edge-MSK` | `192.0.2.16/30` | `2001:db8:ffff:ffff:ffff:ffff:ffff:2/127` |
| `Edge-MSK ↔ Edge-SPB` | `203.0.113.0/30` | `2001:db8:ffff:ffff:ffff:ffff:ffff:0/127` |
| `Edge-MSK ↔ Core-MSK` | `192.0.2.8/30` | `2001:db8:eeee:eeee:eeee:eeee:eeee:0/127` |
| `Edge-SPB ↔ Core-SPB` | `192.0.2.12/30` | `2001:db8:eeee:eeee:eeee:eeee:eeee:2/127` |
| `Core-MSK ↔ CPE-MSK` | `192.0.2.0/30` | — |
| `Core-SPB ↔ CPE-SPB` | `192.0.2.4/30` | — |
| `CPE-MSK local network` | `192.168.10.0/24` | — |
| `CPE-SPB local network` | `192.168.11.0/24` | — |

#### 🔢 IS-IS NET Addresses (AFI `49` — per ISO/IEC 8348)

| Hostname | NET |
|:---|:---|
| `Edge-MSK` | `49.0001.2030.0011.3002.00` |
| `Edge-SPB` | `49.0002.2030.0011.3001.00` |
| `Core-MSK` | `49.0001.1900.0000.2018.00` |
| `Core-SPB` | `49.0002.1900.0000.2013.00` |

#### 🔢 BGP ASN

| Hostname | ASN |
|:---|:---:|
| `Internet` | `65000` |
| `Edge-MSK` | `65001` |
| `Edge-SPB` | `65001` |
| `Core-MSK` | `65001` |
| `Core-SPB` | `65001` |
| `CPE-MSK` | `65010` |
| `CPE-SPB` | `65011` |

---

### 2️⃣ Network Protocols

#### 🔷 IS-IS

| Hostname | Role | Area |
|:---|:---:|:---:|
| `Edge-MSK` | **L1/L2** | `1` |
| `Edge-SPB` | **L1/L2** | `2` |
| `Core-MSK` | **L1** | `1` |
| `Core-SPB` | **L1** | `2` |

 ⚡ **Parameters:**   
 - Metric: **`wide`** (for flexible route cost configuration)   
 - Authentication: **not used**  
 - Hello-interval: **`40` sec**  
 - Connection type: **P2P** 

#### 🟣 BGP

| Device | Routing Table | Screenshot |
|:---|:---|:---|
| `CPE-MSK` | `default only` | [📷 CPE-MSK](screenshots/routing-table/CPE-MSK.png) |
| `CPE-SPB` | `default only` | [📷 CPE-SPB](screenshots/routing-table/CPE-SPB.png) |
| `Edge-MSK` | `full-view` | [📷 Edge-MSK](screenshots/routing-table/Edge-MSK.png) |
| `Edge-SPB` | `full-view` | [📷 Edge-SPB](screenshots/routing-table/Edge-SPB.png) |
| `Core-MSK` | `partial-view` (per route-map) | [📷 CORE-MSK](screenshots/routing-table/CORE-MSK.png) |
| `Core-SPB` | `partial-view` (per route-map) | [📷 CORE-SPB](screenshots/routing-table/CORE-SPB.png) |

 📝 All routers receive a default route for Internet access. For full operation, **PAT** and a static route are configured on the **Internet** router.

 🔄 On **Edge** routers, a **RR** (Route Reflector) is assigned for **Core** routers to bypass the *split horizon* rule (do not advertise eBGP routes if they are received from an iBGP neighbor).

 🛡️ **Route-maps** were applied only in **BGP**.

| Device | Route-map Application | Screenshot |
|:---|:---|:---|
| `Edge-MSK` | Rules towards Core routers | [📷 Edge-MSK](screenshots/route-policy/Edge-MSK.png) |
| `Edge-SPB` | Rules towards Core routers | [📷 Edge-SPB](screenshots/route-policy/Edge-SPB.png) |
| `Core-MSK` | Rules towards CPE routers | [📷 Core-MSK](screenshots/route-policy/Core-MSK.png) |
| `Core-SPB` | Rules towards CPE routers | [📷 Core-SPB](screenshots/route-policy/Core-SPB.png) |

#### 🔥 PAT

 📝 **PAT** is used on clients `CPE-MSK` and `CPE-SPB`, since according to the stated policy, routes from `RFC 1918` are not routed on the Internet.

| Device | Screenshot |
|:---|:---|
| `CPE-MSK` | [📷 PAT](screenshots/PAT/CPE-MSK.png) |
| `CPE-SPB` | [📷 PAT](screenshots/PAT/CPE-SPB.png) |

---

## ✅ Verification

| № | Check | Result |
|:---:|:---|:---|
| `1` | Reachability `CPE-MSK → CPE-SPB` | [📷 traceroute](screenshots/testing/1.png) |
| `2` | Reachability `CPE-SPB → global internet` | [📷 traceroute](screenshots/testing/2.png) |
| `3` | `partial-view` RIB on `Core-MSK` | [📷 RIB](screenshots/routing-table/CORE-MSK.png) |
| `4` | `default-only` RIB on `CPE-SPB` | [📷 RIB](screenshots/routing-table/CPE-SPB.png) |
| `5` | PAT on `CPE-MSK` | [📷 ping](screenshots/testing/5.png) |

---

## 📜 `show run` Output

| Hostname | Configuration |
|:---|:---|
| `Edge-MSK` | [📄 running-config/1.txt](running-config/1.txt) |
| `Edge-SPB` | [📄 running-config/2.txt](running-config/2.txt) |
| `Core-MSK` | [📄 running-config/3.txt](running-config/3.txt) |
| `Core-SPB` | [📄 running-config/4.txt](running-config/4.txt) |
| `CPE-MSK` | [📄 running-config/5.txt](running-config/5.txt) |
| `CPE-SPB` | [📄 running-config/6.txt](running-config/6.txt) |

---

## 🐛 Known Issues

<details>
<summary>🔴 <b>Attached-bit</b> — <i>Core-SPB does not receive L1 LSP ATT</i></summary>

Core-SPB does not receive L1 LSP ATT. Although [Core-MSK](troubles/1/diff_l1) receives it without issues, but they are symmetric in IS-IS configuration.

A full L2 neighbor may be required, but their L2 databases are [identical](troubles/1/same_l2).

I tried connecting Edge-SPB to the Internet and giving it a DG via `default-originate`, since this is the only difference in their routes, but it did not help.

Without a BGP session, Core-SPB will remain without a default route, although it should receive it from L1/L2 with metric `115`.

</details>
