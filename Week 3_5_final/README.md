# Weeks 3-5: Security Engineering, Threat Analysis & Final Report

This directory contains the final engineering submission components, threat simulation analysis, mitigation validations, and administrative structures deployed for the Apex Manufacturing corporate network.

---

## 🛑 1. Security Threat Model: Denial of Service (DoS)

### Attack Vector Analysis
* **Target Node:** Internal Ubuntu Web Server (`192.168.30.100`) sitting inside the restricted Admin segment (VLAN 30).
* **Attacker Profile:** Compromised internal node running Kali Linux (`192.168.10.12`) sitting inside the Sales segment (VLAN 10).
* **Mechanism:** The attacker attempts to exhaust the server's resource buffers by flooding it with high-volume TCP SYN or ICMP Echo Request streams, intending to render corporate internal resources completely unavailable to authorized nodes (such as the Engineering department on VLAN 20).

---

## 🛡️ 2. Architectural Defenses & Mitigation Strategy

Our team established a dual-layer hardware defense to intercept and eliminate these malicious activities:

### Layer 2 Infrastructure Defense (DHCP Snooping)
By activating `ip dhcp snooping`, the switch drops all untrusted DHCP server packets. Clearing the circuit-id parameter (`no ip dhcp snooping information option`) ensures the Cisco 2911 router does not discard legitimate broadcast requests. This prevents rogue systems from hijacking IP generation pools.

### Layer 3 Boundary Protection (Extended Access Control Lists)
An Extended IP Access Control List (`BLOCK_SALES_TO_ADMIN`) is applied directly **inbound** on the router's Sales gateway sub-interface (`GigabitEthernet0/0.10`). 

```text
! Applied Defense Rule Matrix
ip access-list extended BLOCK_SALES_TO_ADMIN
 deny ip 192.168.10.0 0.0.0.255 192.168.30.0 0.0.0.255
 permit ip any any
