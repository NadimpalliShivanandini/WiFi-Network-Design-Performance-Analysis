# Network Configuration

This document describes the main configuration used in the Cisco Packet Tracer Wi-Fi network.

## 1. Router Configuration — R1

R1 provides inter-VLAN routing using router-on-a-stick.

### Physical Interface

```text
interface GigabitEthernet0/0
 no ip address
 no shutdown
```

The physical interface connects R1 to SW1 and carries VLAN traffic through the router subinterfaces.

### Employee VLAN — VLAN 10

```text
interface GigabitEthernet0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0
```

- VLAN: 10
- Name: EMPLOYEE
- Network: 192.168.10.0/24
- Default gateway: 192.168.10.1

### Guest VLAN — VLAN 20

```text
interface GigabitEthernet0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0
```

- VLAN: 20
- Name: GUEST
- Network: 192.168.20.0/24
- Default gateway: 192.168.20.1

---

## 2. DHCP Configuration

R1 provides DHCP services for both wireless networks.

### Employee DHCP Pool

```text
ip dhcp pool EMPLOYEE
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
 dns-server 8.8.8.8
```

### Guest DHCP Pool

```text
ip dhcp pool GUEST
 network 192.168.20.0 255.255.255.0
 default-router 192.168.20.1
 dns-server 8.8.8.8
```

### Excluded Addresses

```text
ip dhcp excluded-address 192.168.10.1 192.168.10.10
ip dhcp excluded-address 192.168.20.1 192.168.20.10
```

The excluded addresses reserve the gateway and other addresses from being assigned dynamically to clients.

---

## 3. Switch Configuration — SW1

SW1 separates Employee and Guest traffic using VLANs.

### VLAN 10 — Employee

```text
vlan 10
 name EMPLOYEE
```

### VLAN 20 — Guest

```text
vlan 20
 name GUEST
```

### Trunk Port — Fa0/1

Fa0/1 connects SW1 to R1 and carries VLAN traffic.

```text
interface FastEthernet0/1
 switchport mode trunk
```

The trunk uses IEEE 802.1Q VLAN tagging.

### Employee Access Port — Fa0/2

Fa0/2 connects SW1 to AP1.

```text
interface FastEthernet0/2
 switchport mode access
 switchport access vlan 10
```

AP1 provides access to the Employee network.

### Guest Access Port — Fa0/3

Fa0/3 connects SW1 to AP2.

```text
interface FastEthernet0/3
 switchport mode access
 switchport access vlan 20
```

AP2 provides access to the Guest network.

---

## 4. Wireless Configuration

Two separate wireless networks were configured using Cisco AP-PT access points.

### AP1 — Employee Network

- SSID: `Company-Employee`
- VLAN: 10
- Frequency: 2.4 GHz
- Channel: 6
- Security: WPA2-PSK
- Encryption: AES

### AP2 — Guest Network

- SSID: `Company-Guest`
- VLAN: 20
- Frequency: 2.4 GHz
- Channel: 11
- Security: WPA2-PSK
- Encryption: AES

The two access points use different 2.4 GHz channels to reduce interference between the wireless networks.

> Wireless passphrases are intentionally not included in this public documentation.

---

## 5. Guest Isolation ACL

An extended ACL was configured to prevent Guest clients from accessing the Employee subnet.

```text
ip access-list extended GUEST_ISOLATION
 deny ip 192.168.20.0 0.0.0.255 192.168.10.0 0.0.0.255
 permit ip any any
```

The ACL is applied inbound to the Guest VLAN interface:

```text
interface GigabitEthernet0/0.20
 ip access-group GUEST_ISOLATION in
```

### ACL Behavior

The following rule blocks traffic from the Guest network to the Employee network:

```text
deny ip 192.168.20.0 0.0.0.255 192.168.10.0 0.0.0.255
```

The following rule permits other traffic that is not blocked by the preceding rule:

```text
permit ip any any
```

---

## 6. Configuration Verification

The following commands were used to verify the network configuration.

### Router Interface Verification

```text
show ip interface brief
```

Expected important interfaces:

```text
GigabitEthernet0/0       up    up
GigabitEthernet0/0.10    up    up
GigabitEthernet0/0.20    up    up
```

### DHCP Verification

```text
show ip dhcp binding
```

This command verifies the IPv4 addresses assigned to wireless clients.

### ACL Verification

```text
show access-lists GUEST_ISOLATION
```

This verifies the Guest isolation ACL and its match counters.

### Switch VLAN Verification

```text
show vlan brief
```

This verifies:

- VLAN 10 — EMPLOYEE
- VLAN 20 — GUEST
- Fa0/2 assigned to VLAN 10
- Fa0/3 assigned to VLAN 20

### Switch Trunk Verification

```text
show interfaces trunk
```

This verifies that Fa0/1 is operating as an IEEE 802.1Q trunk and that VLANs 10 and 20 are active on the trunk.

---

## 7. Configuration Summary

The project combines:

- IPv4 addressing
- DHCP
- VLAN segmentation
- IEEE 802.1Q trunking
- Router-on-a-stick inter-VLAN routing
- IEEE 802.11 wireless networking
- WPA2-PSK/AES wireless security
- 2.4 GHz channel planning
- Extended ACL-based Guest isolation
- Connectivity and configuration verification
