# 🌐 Network Labs

![Labs](https://img.shields.io/badge/Labs-1-blue)
![Status](https://img.shields.io/badge/Status-low--activity-red)

Hands-on network labs emulating real-world ISP and enterprise scenarios.

---

## 📋 Labs

| Lab | Topic | Technologies | Status |
|-----|-------|--------------|--------|
| [Lab1](Lab1/README-RUS.md) | ISP Backbone: Saint-Petersburg — Moscow | BGP, IS-IS, IPv4/IPv6, Route-map, PAT | ✅ Complete |
| [Lab2](Lab2/a) | IS-IS underlay in Saratov | Tunning IS-IS, IPv4/Pv6 | 🟡 In Progress |

---

## 🛠️ Lab Environment

- **Platform:** GNS3, EVE-NG
- **Router images:**  EcoRouter, Cisco IOS XE, MirkoTik, Huawei VRP 

---

## 🎯 About

This repository contains practical network labs focused on routing protocols, 
addressing, and traffic engineering in service provider contexts.

Each lab includes:
- Network topology and addressing schemes
- Protocol configuration and logic
- Verification tests and screenshots
- Running configurations

---

## 📁 Repository Structure

    .
    ├── Lab1/                  # ISP Backbone Lab
    │   ├── README-RUS.md      # Russian description
    │   ├── README-ENG.md      # English description
    │   ├── screenshots/       # Topology, tests, routing tables
    │   ├── running-configs/   # Running configurations
    │   ├── troubles/1/        # Known Issues
    ├── Lab2/                  # lab 2 
    │   ├── a                  # Lab 2, version a - IS-IS underlay
    │   ├── inventory          # list hosts and group_vars
    │   ├── playbooks          # Basic playbooks(get actual config, restore stable version(backup)
    │   ├── README-RUS.md      # Russian description(temporary unavailable)
    │   ├── ansible.cfg        # Basic config for ansible-playbooks
    │   ├── IPAM.md(.yml)      # IP address management(tables) and yml for ansible use
    │   ├── IS-IS              # information about IS-IS routing
    │   ├── IPAM_IP.yml        # information about usage IP for ansible
    │   ├── IPAM_NETWORK.yml   # information about usage IP-segments for ansible(BGP and etc.)
    └── README.md              # This file


---

## 🎯 About me
- Name: Denis
- Education: Ivanovo industrial and economic college, Russia 2/4 course, Graduation Date:2028
- Telegram: [@BrBofTheKali](https://t.me/brbofthekali)
- Email: [denissmex1997@mail.ru](mailto:denissmex1997@mail.ru)
