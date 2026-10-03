# IP Addressing and VLAN Plan

## 1. Network Overview

The network is divided into two IPv4 subnets using VLAN segmentation.

| VLAN | Name | Network | Subnet Mask | Default Gateway |
|---|---|---|---|---|
| 10 | EMPLOYEE | 192.168.10.0/24 | 255.255.255.0 | 192.168.10.1 |
| 20 | GUEST | 192.168.20.0/24 | 255.255.255.0 | 192.168.20.1 |

---

## 2. Router Addressing

| Device | Interface | VLAN | IP Address | Purpose |
|---|---|---:|---|---|
| R1 | G0/0.10 | 10 | 192.168.10.1/24 | Employee gateway |
| R1 | G0/0.20 | 20 | 192.168.20.1/24 | Guest gateway |

R1 uses router-on-a-stick to route traffic between the VLANs.

---

## 3. Switch Port Assignment

| Device | Port | Mode | VLAN | Connected Device |
|---|---|---|---:|---|
| SW1 | Fa0/1 | Trunk | 1, 10, 20 | R1 |
| SW1 | Fa0/2 | Access | 10 | AP1 |
| SW1 | Fa0/3 | Access | 20 | AP2 |

The trunk between SW1 and R1 uses IEEE 802.1Q VLAN tagging.

---

## 4. Wireless Network Addressing

| Access Point | SSID | VLAN | Network | Gateway |
|---|---|---:|---|---|
| AP1 | Company-Employee | 10 | 192.168.10.0/24 | 192.168.10.1 |
| AP2 | Company-Guest | 20 | 192.168.20.0/24 | 192.168.20.1 |

---

## 5. DHCP Configuration

DHCP is provided by R1.

### Employee Network

- Network: `192.168.10.0/24`
- Default gateway: `192.168.10.1`
- DNS server: `8.8.8.8`
- Reserved addresses: `192.168.10.1 - 192.168.10.10`

### Guest Network

- Network: `192.168.20.0/24`
- Default gateway: `192.168.20.1`
- DNS server: `8.8.8.8`
- Reserved addresses: `192.168.20.1 - 192.168.20.10`

---

## 6. Wireless Channel Plan

| Access Point | SSID | Frequency | Channel |
|---|---|---|---:|
| AP1 | Company-Employee | 2.4 GHz | 6 |
| AP2 | Company-Guest | 2.4 GHz | 11 |

Different 2.4 GHz channels were selected for the two access points to reduce overlapping-channel interference.

---

## 7. Example DHCP Leases

During testing, the following client addresses were observed:

| Client | Network | Example IP Address |
|---|---|---|
| Laptop-HR1 | Employee | 192.168.10.11 |
| Laptop-HR2 | Employee | 192.168.10.12 |
| Laptop-IT1 | Guest | 192.168.20.12 |
| Laptop-IT2 | Guest | 192.168.20.13 |

The exact client IP addresses may change if the DHCP leases are renewed or the Packet Tracer simulation is restarted.

---

## 8. Network Segmentation

The two wireless networks are logically separated:

```text
VLAN 10
EMPLOYEE
192.168.10.0/24
        |
        |  R1 G0/0.10
        |
       R1
        |
        |  R1 G0/0.20
        |
VLAN 20
GUEST
192.168.20.0/24
```

An extended ACL named `GUEST_ISOLATION` prevents traffic originating from the Guest subnet from reaching the Employee subnet.
