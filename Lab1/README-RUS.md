# 🌐 Описание проделанной работы

---

## 🎯 Поставленная задача

Существует операторский backbone для связи между Санкт-Питербургом и Москвой. В каждом городе есть корневой маршрутизатор (**core-маршрутизатор**).

Необходимо обеспечить:
- ✅ Маршрутизацию между 2 клиентами из разных городов, которым выделены специальные адреса и номера AS
- ✅ Доступ в Интернет

Для работы необходимо поднять протоколы **BGP** и **IS-IS**, настроить **IPv4** и **IPv6** адресацию, организовать простые **route-map** для фильтрации маршрутов.

&gt; 📌 Для маршрутизации между роутерами были использованы специальные адреса (`RFC 5737` & `RFC 3849` & `RFC 1918`), под ASN выделен частный диапазон (`RFC 6996`).

---

## 🖥️ Использованные образы маршрутизаторов

| Роль | Образ | Версия |
|:---:|:---|:---|
| 🟢 Edge | **EcoRouterOS** | `3.2.6.2` |
| 🔵 Core | **Cisco IOS XE** | `16.07.01` |
| 🟡 CPE (client) | **MikroTik RouterOS** | `7.8` |
| 🌐 Internet | **Cisco IOS** | `15.3` |

---

## 🗺️ Топология и адресация

| Документ | Описание |
|:---|:---|
| 📊 [Topology](screenshots/Topology.png) | Топология сети |
| 🌍 [IPv4](screenshots/IPv4.png) | IP-сегменты для связи |
| 🌐 [IS-IS](screenshots/IS-IS.png) | Схема с ролями, зонами и NET-адресами |
| 🔒 [IPv4Filter](screenshots/IPv4Filter.png) | Логика фильтрации трафика (IPv4) |
| 🔒 [IPv6Filter](screenshots/IPv6Filter.png) | Логика фильтрации трафика (IPv6) |
| 🌍 [IPv6](screenshots/IPv6.png) | IPv6-адресация |

---

## ⚙️ Настройка

### 1️⃣ Адресное пространство

#### 🔗 Линки между устройствами

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

#### 🔢 IS-IS NET-адреса (AFI `49` — согласно ISO/IEC 8348)

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

### 2️⃣ Сетевые протоколы

#### 🔷 IS-IS

| Hostname | Role | Area |
|:---|:---:|:---:|
| `Edge-MSK` | **L1/L2** | `1` |
| `Edge-SPB` | **L1/L2** | `2` |
| `Core-MSK` | **L1** | `1` |
| `Core-SPB` | **L1** | `2` |

&gt; ⚡ **Параметры:**   
&gt; - Метрика: **`wide`** (для гибкой настройки стоимости маршрута)   
&gt; - Аутентификация: **не используется**  
&gt; - Hello-interval: **`40` сек**  
&gt; - Тип соединения: **P2P** 

#### 🟣 BGP

| Устройство | Таблица маршрутизации | Скриншот |
|:---|:---|:---|
| `CPE-MSK` | `default only` | [📷 CPE-MSK](screenshots/routing-table/CPE-MSK.png) |
| `CPE-SPB` | `default only` | [📷 CPE-SPB](screenshots/routing-table/CPE-SPB.png) |
| `Edge-MSK` | `full-view` | [📷 Edge-MSK](screenshots/routing-table/Edge-MSK.png) |
| `Edge-SPB` | `full-view` | [📷 Edge-SPB](screenshots/routing-table/Edge-SPB.png) |
| `Core-MSK` | `partial-view` (согласно route-map) | [📷 CORE-MSK](screenshots/routing-table/CORE-MSK.png) |
| `Core-SPB` | `partial-view` (согласно route-map) | [📷 CORE-SPB](screenshots/routing-table/CORE-SPB.png) |

&gt; 📝 Все маршрутизаторы получают маршрут по умолчанию для выхода в Интернет. Для полноценной работы на маршрутизаторе **Internet** стоит **PAT** и статический маршрут.

&gt; 🔄 На **Edge** роутерах для **Core** маршрутизаторов назначен **RR** (Route Reflector) для обхода правила *split horizon* (не передавать eBGP маршруты, если они получены от iBGP соседа).

&gt; 🛡️ **Route-map** применялись только в **BGP**.

| Устройство | Применение route-map | Скриншот |
|:---|:---|:---|
| `Edge-MSK` | Правила по отношению к Core-маршрутизаторам | [📷 Edge-MSK](screenshots/route-policy/Edge-MSK.png) |
| `Edge-SPB` | Правила по отношению к Core-маршрутизаторам | [📷 Edge-SPB](screenshots/route-policy/Edge-SPB.png) |
| `Core-MSK` | Правила по отношению к CPE-роутерам | [📷 Core-MSK](screenshots/route-policy/Core-MSK.png) |
| `Core-SPB` | Правила по отношению к CPE-роутерам | [📷 Core-SPB](screenshots/route-policy/Core-SPB.png) |

#### 🔥 PAT

&gt; 📝 На клиентах `CPE-MSK` и `CPE-SPB` используется правило **PAT**, т.к. согласно заявленной политике маршруты из `RFC 1918` не маршрутизируются в Интернете.

| Устройство | Скриншот |
|:---|:---|
| `CPE-MSK` | [📷 PAT](screenshots/PAT/CPE-MSK.png) |
| `CPE-SPB` | [📷 PAT](screenshots/PAT/CPE-SPB.png) |

---

## ✅ Проверка работоспособности

| № | Проверка | Результат |
|:---:|:---|:---|
| `1` | Доступность `CPE-MSK → CPE-SPB` | [📷 traceroute](screenshots/testing/1.png) |
| `2` | Доступность `CPE-SPB → global internet` | [📷 traceroute](screenshots/testing/2.png) |
| `3` | `partial-view` RIB на `Core-MSK` | [📷 RIB](screenshots/routing-table/CORE-MSK.png) |
| `4` | `default-only` RIB на `CPE-SPB` | [📷 RIB](screenshots/routing-table/CPE-SPB.png) |
| `5` | PAT на `CPE-MSK` | [📷 ping](screenshots/testing/5.png) |

---

## 📜 Вывод `show run`

| Hostname | Конфигурация |
|:---|:---|
| `Edge-MSK` | [📄 running-config/1.txt](running-config/1.txt) |
| `Edge-SPB` | [📄 running-config/2.txt](running-config/2.txt) |
| `Core-MSK` | [📄 running-config/3.txt](running-config/3.txt) |
| `Core-SPB` | [📄 running-config/4.txt](running-config/4.txt) |
| `CPE-MSK` | [📄 running-config/5.txt](running-config/5.txt) |
| `CPE-SPB` | [📄 running-config/6.txt](running-config/6.txt) |

---

## 🐛 Известные проблемы

<details>
<summary>🔴 <b>Attached-bit</b> — <i>Core-SPB не получает L1 LSP ATT</i></summary>

Core-SPB не получает в L1 LSP ATT. Хотя его спокойно получает [Core-MSK](troubles/1/diff_l1), но они и симметричны по настройке относительно IS-IS.

Возможно, нужен полноценный L2 сосед, но L2 database у них [одинаковая](troubles/1/same_l2).

Пытался подключать Edge-SPB в Internet и выдавать ему DG через `default-originate`, ибо это единственное чем они отличаются по маршрутам, но не помогло.

Без BGP сессии Core-SPB останется без default маршрута, хотя он должен получать ее от L1/L2 с метрикой `115`.

</details>
