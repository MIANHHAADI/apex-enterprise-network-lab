# Week 1: Requirements Analysis & Network Design Blueprint

This directory contains the initial planning phase documentation, structural system designs, and risk parameters formulated for the Apex Manufacturing enterprise infrastructure.

---

## 📊 1. IP Addressing & Segmentation Scheme

The enterprise is segmented using an explicit Class C subnetting architecture to minimize traffic leakage, optimize bandwidth performance, and simplify firewall rule structures.

| Department | Assigned VLAN | Network Base Subnet | Subnet Mask | Usable Gateway IP | Dynamic Pool Range (DHCP) | Excluded Admin Range | Assigned Static Targets |
| :--- | :---: | :--- | :--- | :--- | :--- | :--- | :--- |
| **Sales** | 10 | `192.168.10.0/24` | `255.255.255.0` | `192.168.10.1` | `192.168.10.11` - `192.168.10.254` | `192.168.10.1` - `192.168.10.10` | None |
| **Engineering**| 20 | `192.168.20.0/24` | `255.255.255.0` | `192.168.20.1` | `192.168.20.11` - `192.168.20.254` | `192.168.20.1` - `192.168.20.10` | None |
| **Admin** | 30 | `192.168.30.0/24` | `255.255.255.0` | `192.168.30.1` | `192.168.30.11` - `192.168.30.254` | `192.168.30.1` - `192.168.30.10` | `192.168.30.100` (Ubuntu Web Server) |

### Design Strategy Notes:
* **Administrative Exclusions:** IPs from `.1` to `.10` are explicitly reserved in each pool for default gateways, potential future auxiliary equipment, or static hardware targets.
* **VLAN Tagging:** Data packets flowing outside their native switchports are dynamically stamped with an 802.1Q hardware identifier header before being passed up the Trunk link.

---

## 🛡️ 2. Project Security Risk Matrix

Prior to physical deployment, the network engineering team identified potential vulnerabilities in the required architecture and established engineering mitigations.

| Vulnerability Target | Threat Vector | Impact Severity | Probability | Applied Technical Mitigation Strategy | Engineering Owner |
| :--- | :--- | :---: | :---: | :--- | :--- |
| **Layer 2 Access Layer** | Rogue DHCP spoofing attacks or malicious IP leakage inside segments. | **High** | Medium | Implement global IP DHCP Snooping on the switch. Trust only the upstream interface (`Gig0/1`) leading to the true routing gateway. | **Malik Mauvia Ahmed** |
| **Cisco Switch IOS** | Cisco Option 82 structural dropping bug on DHCP transactions. | **High** | High | Clear Option 82 data generation dynamically at the switch access level using the `no ip dhcp snooping information option` parameter. | **Malik Mauvia Ahmed** |
| **Layer 3 Routing Core** | Unauthorized cross-department segment navigation from Sales to Admin. | **Critical** | High | Design and compile an Extended IP Access Control List (ACL) tracking source-to-destination protocols, applied `inbound` directly to the `Gig0/0.10` interface. | **Abdul Wahab** |
| **GitHub Workflow** | Code overrides or accidental main branch configuration deletions. | **Medium** | Low | Establish clean directory boundaries and have all updates pass through testing validation checklists. | **Malik Mansoor Hayat** |
| **Administrative Framework** | Missing team roles due to optimized 4-man student grouping constraints. | **Medium** | Low | Dual-allocation of duties; strict tracking of tasks, tracking milestones, and status reports. | **Abdul Hadi** |

---

## 📅 Week 1 Achievement Metrics
* Approved operational team charter mandates and signatures.
* Formulated logical loop-free layout structures using draw.io vectors.
* Subnet calculations executed and locked against address overlap vulnerabilities.
