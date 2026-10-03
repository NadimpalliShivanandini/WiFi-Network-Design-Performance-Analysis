# Testing and Validation Results

The network was tested in Cisco Packet Tracer after completing the configuration.

## 1. DHCP Validation

The router successfully assigned IPv4 addresses to four wireless clients.

| Client | Network | Example Assigned IP |
|---|---|---|
| Laptop-HR1 | Employee | 192.168.10.11 |
| Laptop-HR2 | Employee | 192.168.10.12 |
| Laptop-IT1 | Guest | 192.168.20.12 |
| Laptop-IT2 | Guest | 192.168.20.13 |

DHCP leases were verified using:

```text
show ip dhcp binding
```

## 2. Employee Connectivity

Laptop-HR1 successfully reached the Employee default gateway.

```text
ping 192.168.10.1
```

Result:

- Sent: 4
- Received: 4
- Lost: 0
- Packet loss: 0%

The Employee client also successfully reached the Guest VLAN gateway:

```text
ping 192.168.20.1
```

Result:

- Sent: 4
- Received: 4
- Lost: 0
- Packet loss: 0%

This confirms that inter-VLAN routing is functioning.

## 3. Guest Connectivity

Laptop-IT2 successfully reached its Guest default gateway:

```text
ping 192.168.20.1
```

Result:

- Sent: 4
- Received: 4
- Lost: 0
- Packet loss: 0%

## 4. Guest-to-Employee Isolation

Guest traffic was tested against the Employee gateway:

```text
ping 192.168.10.1
```

The Guest client received no successful replies and the test showed 100% packet loss.

This confirms that the `GUEST_ISOLATION` ACL is blocking traffic from the Guest subnet toward the Employee subnet.

## 5. ACL Verification

The ACL was verified using:

```text
show access-lists GUEST_ISOLATION
```

The configured rules were:

```text
deny ip 192.168.20.0 0.0.0.255 192.168.10.0 0.0.0.255
permit ip any any
```

The deny rule recorded matches during testing, confirming that Guest-to-Employee traffic was processed by the ACL.

## 6. Switch Validation

The following commands were used to verify the switch configuration:

```text
show vlan brief
show interfaces trunk
```

Results confirmed:

- VLAN 10 `EMPLOYEE` assigned to Fa0/2
- VLAN 20 `GUEST` assigned to Fa0/3
- Fa0/1 operating as an 802.1Q trunk
- VLANs 10 and 20 active on the trunk

## Overall Result

The Cisco Packet Tracer simulation successfully demonstrated:

- Wireless client connectivity
- DHCP address assignment
- VLAN segmentation
- Router-on-a-stick inter-VLAN routing
- WPA2-PSK wireless security
- Guest-to-Employee network isolation
- VLAN and trunk verification
