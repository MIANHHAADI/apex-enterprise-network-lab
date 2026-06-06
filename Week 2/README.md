# Week 2: Platform Deployment & Base Cisco Configurations

This directory houses the working Cisco running configuration files and network baseline data implemented for the Apex Manufacturing enterprise layout.

---

## 🎛️ 1. Cisco 2911 Router Running Configuration

The core routing gateway manages Inter-VLAN routing via IEEE 802.1Q sub-interfaces, serves dynamic IP scopes, and drops unauthorized traffic patterns via an inbound Access Control List.

```text
! --- CISCO 2911 ROUTER RUNNING CONFIGURATION LOG ---
enable
configure terminal

interface GigabitEthernet0/0
 description UPLINK_TO_CORE_SWITCH
 no shutdown
exit

interface GigabitEthernet0/0.10
 description SALES_GATEWAY
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0
exit

interface GigabitEthernet0/0.20
 description ENGINEERING_GATEWAY
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0
exit

interface GigabitEthernet0/0.30
 description ADMIN_GATEWAY
 encapsulation dot1Q 30
 ip address 192.168.30.1 255.255.255.0
exit

ip dhcp excluded-address 192.168.10.1 192.168.10.10
ip dhcp excluded-address 192.168.20.1 192.168.20.10
ip dhcp excluded-address 192.168.30.1 192.168.30.10

ip dhcp pool SALES_POOL
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
exit

ip dhcp pool ENG_POOL
 network 192.168.20.0 255.255.255.0
 default-router 192.168.20.1
exit

ip dhcp pool ADMIN_POOL
 network 192.168.30.0 255.255.255.0
 default-router 192.168.30.1
exit

ip access-list extended BLOCK_SALES_TO_ADMIN
 deny ip 192.168.10.0 0.0.0.255 192.168.30.0 0.0.0.255
 permit ip any any
exit

interface GigabitEthernet0/0.10
 ip access-group BLOCK_SALES_TO_ADMIN in
exit

end
write memory

! --- CISCO 2960 SWITCH RUNNING CONFIGURATION LOG ---
enable
configure terminal

vlan 10
 name Sales
vlan 20
 name Engineering
vlan 30
 name Admin
exit

interface range FastEthernet0/1 - 2
 description SALES_ACCESS_PORTS
 switchport mode access
 switchport access vlan 10
exit

interface FastEthernet0/10
 description ENGINEERING_ACCESS_PORT
 switchport mode access
 switchport access vlan 20
exit

interface range FastEthernet0/20 - 21
 description ADMIN_ACCESS_PORTS
 switchport mode access
 switchport access vlan 30
exit

interface GigabitEthernet0/1
 description TRUNK_UPLINK_TO_ROUTER
 switchport mode trunk
exit

ip dhcp snooping
ip dhcp snooping vlan 10,20,30
no ip dhcp snooping information option

interface GigabitEthernet0/1
 ip dhcp snooping trust
exit

end
write memory
