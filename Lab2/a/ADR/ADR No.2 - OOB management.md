<details>
<summary>ADR-002: Out-of-Band Management для автоматизированного внедрения IS-IS метрик [RUS]</summary>

| Поле | Значение |
|:---|:---|
| **Статус** | ✅ **Принято (Approved)** |
| **Дата** | 2026-07-06 |
| **Зависимости** | IPAM, Ansible Playbooks |

---

## 1. Контекст и проблема

В рамках автоматизации внедрения IS-IS метрик (cost) и их типов выявлен критический блокер:

> **При одностороннем изменении параметров на линке adjacency IS-IS разрывается, что приводит к потере SSH-сессии и прерыванию playbooks.**

Данная проблема делает недопустимым применение изменений «в лоб» на production-линках без резервного канала управления.

---

## 2. Рассмотренные варианты

### 2.1. Вариант A: Последовательное внедрение через резервный линк

| Параметр | Оценка |
|:---|:---|
| **Описание** | Изменения выполняются по очереди, начиная с PE-VOLGSKIY. Подключение к узлу осуществляется через соседний PE (PE-KIROVCKIY), метрика меняется на одной стороне, затем — на второй. |
| **Проблема EDGE-SARATOV** | На линке `EDGE-SARATOV ↔ CORE-SARATOV` связь неизбежно обрывается — playbook завершается с ошибкой. |
| **Масштабируемость** | ❌ Низкая. Требуется дублирование IPAM с альтернативным сегментом поверх физических/субинтерфейсов. |
| **Сложность** | 🔴 Высокая. Ручная оркестрация порядка изменений, зависимость от текущей топологии. |
| **Вердикт** | **Отклонено** |

### 2.2. Вариант B: Out-of-Band Management (OOB)

| Параметр | Оценка |
|:---|:---|
| **Описание** | Выделение изолированной физической сети управления на базе L3-коммутатора и отдельной подсети `172.16.100.0/24`. |
| **Изоляция** | ✅ VRF-lite на каждом роутере предотвращает попадание OOB-префиксов в production RIB. Критично для PE с клиентскими RFC1918. |
| **Надёжность** | ✅ Независимость от состояния production-линков. |
| **Стоимость** | 🟡 Требуются дополнительные кабели и L3-свитч. |
| **Вердикт** | **Принято** |

---

## 3. Принятое решение

**Реализовать Out-of-Band management (Вариант B).**

### 3.1. Адресное пространство

```text
Сеть:      172.16.100.0/24
Gateway:   172.16.100.253 (L3-switch)
VRF:       MGMT-OOB
VLAN:      100
```

### 3.2. Подключение

Каждый роутер подключается отдельным кабелем к L3-свитчу в VLAN 100. PE-VOLGSKIY, PE-KIROVCKIY, CORE-SARATOV и EDGE-SARATOV получают адреса из сегмента `172.16.100.0/24` в выделенном VRF MGMT-OOB.

---

## 4. Влияние на смежные системы

| Аспект | Влияние | Комментарий |
|:---|:---|:---|
| **Безопасность** | 🟢 Положительное | Изолированная VRF + VLAN исключают пересечение трафика управления с production. |
| **Производительность** | 🟡 Незначительное | Дополнительная RIB/FIB в VRF MGMT-OOB. Нагрузка минимальна, не требует апгрейда железа. |
| **Операционка** | 🟢 Положительное | Возможность аварийного доступа при отказе любого production-линка. |
| **Клиенты PE** | 🟢 Нейтральное | VRF-lite гарантирует, что `172.16.100.0/24` не попадёт в клиентские VRF/RIB. |

---

## 5. План внедрения

| Этап | Действие |  Критерий готовности |
|:---|:---|:---|
| **1** | Подключить L3-свитч, настроить **VLAN 100** на портах доступа | `show vlan brief` — VLAN 100 active |
| **2** | На роутерах подготовить интерфейсы + **VRF MGMT-OOB** с адресацией из `172.16.100.0/24` | `show ip route vrf MGMT-OOB` — виден connected |
| **3** | На PE настроить дополнительный **SSH-сервер** в VRF mgmt (роутеры пустые, доступ извне ещё не настроен) |  `ssh -vrf MGMT-OOB` успешен |
| **4** | Прокладка кабелей: роутер → L3-свитч (порты VLAN 100) | Link-up на всех портах управления |
| **5** | Валидация: проверка связности `ping 172.16.100.253 vrf MGMT-OOB` со всех узлов |  100% успешных ICMP |

---

## 6. Последствия

### ✅ Позитивные
- Изолированная система управления, независимая от production-топологии.
- Повышение безопасности за счёт сегментации (VRF + VLAN).
- Сохранение управляемости при отказе любого транспортного линка.
- Возможность удалённой настройки любых параметров, включая IS-IS, без риска потери сессии.

### ⚠️ Негативные / Затраты
- Расход запасов кабеля на физические подключения.
- Закупка выделенного L3-коммутатора (рекомендуется базовая Cisco с поддержкой VRF-lite).
- Незначительное увеличение операционной сложности.
</details>
<details>  
<summary>ADR-002: Out-of-Band Management for IS-IS Metric Deployment [ENG]</summary>

| Field | Value |
|:---|:---|
| **Status** | ✅ **Approved** |
| **Date** | 2026-07-06 |
| **Dependencies** | IPAM, Ansible Playbooks |

---

## 1. Context and Problem

We are automating IS-IS metric (cost) deployment. We found a big problem:

> **If we change settings on one side of a link, the IS-IS adjacency breaks. SSH session drops. The playbook stops.**

We cannot change production links directly. We need a backup management channel.

---

## 2. Options We Checked

### 2.1. Option A: Step-by-Step Deployment via Backup Link

| Point | Details |
|:---|:---|
| **What is it** | We change settings one by one. Start with PE-VOLGSKIY. Connect through PE-KIROVCKIY. Change metric on one side, then on the other side. |
| **Problem with EDGE-SARATOV** | On the link `EDGE-SARATOV ↔ CORE-SARATOV` the connection will break anyway. Playbook will fail. |
| **Scale** | ❌ Bad. We need to copy the IPAM table with a new segment on top of physical interfaces. |
| **Complexity** | 🔴 High. Manual order of changes. Depends on current network map. |
| **Decision** | **Rejected** |

### 2.2. Option B: Out-of-Band Management (OOB)

| Point | Details |
|:---|:---|
| **What is it** | Separate physical network for management. Uses an L3 switch and subnet `172.16.100.0/24`. |
| **Isolation** | ✅ VRF-lite on each router stops OOB routes from entering production RIB. Important for PE routers with customer RFC1918 IPs. |
| **Reliability** | ✅ Does not depend on production links. |
| **Cost** | 🟡 Needs extra cables and an L3 switch. |
| **Decision** | **Accepted** |

---

## 3. Our Decision

**We will use Out-of-Band management (Option B).**

### 3.1. IP Plan

```text
Network:   172.16.100.0/24
Gateway:   172.16.100.253 (L3 switch)
VRF:       MGMT-OOB
VLAN:      100
```

### 3.2. How We Connect

Each router connects with a separate cable to the L3 switch in VLAN 100. PE-VOLGSKIY, PE-KIROVCKIY, CORE-SARATOV and EDGE-SARATOV get IPs from `172.16.100.0/24` inside VRF MGMT-OOB.

---

## 4. Impact on Other Systems

| Area | Impact | Comment |
|:---|:---|:---|
| **Security** | 🟢 Good | Separate VRF + VLAN stop bad traffic from moving between management and production. |
| **Performance** | 🟡 Small | Extra RIB/FIB in VRF MGMT-OOB. Load is tiny, no hardware upgrade needed. |
| **Operations** | 🟢 Good | Emergency access works even if a production link is down. |
| **PE Customers** | 🟢 Neutral | VRF-lite makes sure `172.16.100.0/24` does not leak into customer VRF/RIB. |

---

## 5. Deployment Plan

| Step | Action | Done When |
|:---|:---|:---|
| **1** | Connect L3 switch. Set up **VLAN 100** on access ports. | `show vlan brief` shows VLAN 100 active |
| **2** | On routers: create interfaces + **VRF mgmt** with IPs from `172.16.100.0/24` | `show ip route vrf MGMT-OOB` shows connected route |
| **3** | On PE routers: turn on extra **SSH server** in VRF MGMT-OOB (routers are empty, no outside access yet) |  `ssh -vrf MGMT-OOB` works |
| **4** | Run cables: router → L3 switch (ports in VLAN 100) | Link-up on all management ports |
| **5** | Test: run `ping 172.16.100.253 vrf MGMT-OOB` from all devices | 100% ICMP success |

---

## 6. Results

### ✅ Good
- Separate management system. Does not depend on production network.
- Better security because of separation (VRF + VLAN).
- We keep control even if a transport link fails.
- We can change any settings remotely, including IS-IS, without losing the session.

### ⚠️ Bad / Costs
- We spend cable stock for physical connections.
- We need to buy a separate L3 switch (basic Cisco with VRF-lite support is fine).
- A bit more operational work.
</details>