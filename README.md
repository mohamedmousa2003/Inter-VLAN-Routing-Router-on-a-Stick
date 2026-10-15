<h1 align="center">Inter-VLAN Routing Using Router-on-a-Stick</h1>

<p align="center">
  A Cisco Packet Tracer lab that segments a network into three VLANs (HR, Marketing, Development) and enables communication between them using a router with sub-interfaces over a trunk link.
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Cisco-Packet%20Tracer-1BA0D7?style=for-the-badge&logo=cisco&logoColor=white" alt="Cisco Packet Tracer"/>
  <img src="https://img.shields.io/badge/Switching-VLAN%20%2B%20Trunk-purple?style=for-the-badge" alt="VLAN and Trunk"/>
  <img src="https://img.shields.io/badge/Routing-Inter--VLAN-blue?style=for-the-badge" alt="Inter-VLAN Routing"/>
  <img src="https://img.shields.io/badge/Level-CCNA-green?style=for-the-badge" alt="CCNA"/>
</p>

---

## Table of Contents

- [Overview](#overview)
- [Network Topology](#network-topology)
- [VLAN and Port Assignment](#vlan-and-port-assignment)
- [IP Addressing Table](#ip-addressing-table)
- [Configuration](#configuration)
- [Verification and Testing](#verification-and-testing)
- [Screenshots](#screenshots)
- [Repository Structure](#repository-structure)
- [How to Run](#how-to-run)
- [Skills Demonstrated](#skills-demonstrated)
- [Author](#author)

---

## Overview

This project builds on a single-switch VLAN design. The 24 FastEthernet ports are divided into three VLANs using **access mode**, and the switch is connected to a router through a **trunk link**. The router uses **sub-interfaces** (Router-on-a-Stick) with **802.1Q encapsulation** to route traffic between the VLANs.

**Objectives**

- Create VLAN 10 (HR), VLAN 20 (Marketing), and VLAN 30 (Development).
- Assign switch ports to VLANs using access mode.
- Configure a trunk port between the switch and the router.
- Configure router sub-interfaces as default gateways for each VLAN.
- Verify connectivity between devices in different VLANs.

**How Router-on-a-Stick works**

- One physical router interface (Gig0/0) is divided into logical sub-interfaces, one per VLAN.
- Each sub-interface is tagged with its VLAN ID using `encapsulation dot1Q`.
- The switch trunk port carries tagged traffic for all VLANs to the router.
- The router routes packets between VLANs and sends them back over the same trunk.

---

## Network Topology

| Component   | Quantity | Model           |
|-------------|----------|-----------------|
| Router      | 1        | Cisco 2911      |
| Switch      | 1        | Cisco 2960-24TT |
| End Devices | 4        | Laptop-PT       |

```
                          [ Router ]
                           Gig0/0
                              |
                              |  Trunk (802.1Q)
                              |
                           Gig0/1
                          [ Switch0 ]
              ____________/    |    \____________
             /                 |                 \
       VLAN 10 (HR)    VLAN 20 (Marketing)   VLAN 30 (Development)
        /       \              |                    |
  [Laptop0]  [Laptop1]     [Laptop2]            [Laptop3]
```

---

## VLAN and Port Assignment

| VLAN ID | VLAN Name   | Ports           | Mode   | Connected Devices |
|---------|-------------|-----------------|--------|-------------------|
| 10      | HR          | Fa0/1 - Fa0/8   | Access | Laptop0, Laptop1  |
| 20      | Marketing   | Fa0/9 - Fa0/16  | Access | Laptop2           |
| 30      | Development | Fa0/17 - Fa0/24 | Access | Laptop3           |
| All     | Management  | Gig0/1          | Trunk  | Router (Gig0/0)   |

---

## IP Addressing Table

| Device  | Interface | VLAN | IP Address   | Subnet Mask   | Default Gateway |
|---------|-----------|------|--------------|---------------|-----------------|
| Router  | Gig0/0.10 | 10   | 192.168.10.1 | 255.255.255.0 | N/A             |
| Router  | Gig0/0.20 | 20   | 192.168.20.1 | 255.255.255.0 | N/A             |
| Router  | Gig0/0.30 | 30   | 192.168.30.1 | 255.255.255.0 | N/A             |
| Laptop0 | Fa0       | 10   | 192.168.10.2 | 255.255.255.0 | 192.168.10.1    |
| Laptop1 | Fa0       | 10   | 192.168.10.3 | 255.255.255.0 | 192.168.10.1    |
| Laptop2 | Fa0       | 20   | 192.168.20.2 | 255.255.255.0 | 192.168.20.1    |
| Laptop3 | Fa0       | 30   | 192.168.30.2 | 255.255.255.0 | 192.168.30.1    |

---

## Configuration

### Switch: Create VLANs

```
Switch(config)# vlan 10
Switch(config-vlan)# name HR
Switch(config-vlan)# exit
Switch(config)# vlan 20
Switch(config-vlan)# name Marketing
Switch(config-vlan)# exit
Switch(config)# vlan 30
Switch(config-vlan)# name Development
Switch(config-vlan)# exit
```

### Switch: Assign Ports in Access Mode

```
Switch(config)# interface range fastEthernet 0/1 - 8
Switch(config-if-range)# switchport mode access
Switch(config-if-range)# switchport access vlan 10
Switch(config-if-range)# exit

Switch(config)# interface range fastEthernet 0/9 - 16
Switch(config-if-range)# switchport mode access
Switch(config-if-range)# switchport access vlan 20
Switch(config-if-range)# exit

Switch(config)# interface range fastEthernet 0/17 - 24
Switch(config-if-range)# switchport mode access
Switch(config-if-range)# switchport access vlan 30
Switch(config-if-range)# exit
```

### Switch: Configure the Trunk Port

```
Switch(config)# interface gigabitEthernet 0/1
Switch(config-if)# switchport mode trunk
Switch(config-if)# exit
```

### Router: Configure Sub-interfaces

```
Router(config)# interface gigabitEthernet 0/0
Router(config-if)# no shutdown
Router(config-if)# exit

Router(config)# interface gigabitEthernet 0/0.10
Router(config-subif)# encapsulation dot1Q 10
Router(config-subif)# ip address 192.168.10.1 255.255.255.0
Router(config-subif)# exit

Router(config)# interface gigabitEthernet 0/0.20
Router(config-subif)# encapsulation dot1Q 20
Router(config-subif)# ip address 192.168.20.1 255.255.255.0
Router(config-subif)# exit

Router(config)# interface gigabitEthernet 0/0.30
Router(config-subif)# encapsulation dot1Q 30
Router(config-subif)# ip address 192.168.30.1 255.255.255.0
Router(config-subif)# exit
```

> The physical interface `Gig0/0` has no IP address. It only needs `no shutdown` so the sub-interfaces can come up.

### Steps Performed

1. Created three VLANs and named them by department.
2. Configured access ports and assigned them to their VLANs.
3. Configured the switch uplink as a trunk port.
4. Created router sub-interfaces with 802.1Q encapsulation and assigned the gateway IP for each VLAN.
5. Assigned static IP addresses and default gateways to the laptops.
6. Verified the configuration and tested connectivity between VLANs.

---

## Verification and Testing

| Command | Purpose |
|---------|---------|
| `show vlan brief` | Verify VLANs, names, and assigned access ports |
| `show interfaces trunk` | Confirm Gig0/1 is trunking and the VLANs it carries |
| `show ip interface brief` | Verify sub-interfaces are up/up with the correct IPs |
| `show ip route` | Confirm the connected networks (`C`) for each VLAN |
| `ping <destination-ip>` | Test connectivity within and between VLANs |

**Results**

- Devices in the **same VLAN** communicate successfully.
- Devices in **different VLANs** now communicate successfully through the router, unlike the previous lab where VLANs were isolated.
- The first ping between VLANs may show a timeout while ARP resolves, and the following pings succeed.

---

## Screenshots

<table>
  <tr>
    <td align="center">
      <img width="1081" height="573" alt="Network topology with router and three VLANs" src="https://github.com/user-attachments/assets/228c0c1d-f8aa-4302-92f7-9d9fe183ddad" />
      <br/><sub><b>Network Topology</b></sub>
    </td>
  </tr>
  <tr>
    <td align="center">
      <img width="722" height="233" alt="show vlan brief output" src="https://github.com/user-attachments/assets/6b9621c6-269a-4eb2-b083-acd9afbddc9d" />
      <br/><sub><b>VLAN Table (show vlan brief)</b></sub>
    </td>
  </tr>
  <tr>
    <td align="center">
      <img width="692" height="210" alt="show interfaces trunk output" src="https://github.com/user-attachments/assets/98fcbcc7-fbc8-44a0-8983-9b261b273117" />
      <br/><sub><b>Trunk Port (show interfaces trunk)</b></sub>
    </td>
  </tr>
  <tr>
    <td align="center">
      <img width="717" height="178" alt="Router sub-interfaces status" src="https://github.com/user-attachments/assets/17940b38-93f0-4564-9e95-04d9fa51a246" />
      <br/><sub><b>Router Sub-interfaces (show ip interface brief)</b></sub>
    </td>

  <tr>
    <td align="center">
      <img width="1597" height="690" alt="Ping Laptop0 to Laptop1 , Laptop2 and Laptop3" src="https://github.com/user-attachments/assets/5f6a7470-eefe-43d0-9292-5d611445aa6c" />
      <br/><sub><b>Ping Test: Laptop0 to Laptop1 , Laptop2 and Laptop3 (Inter-VLAN)</b></sub>
    </td>
  </tr>
</table>

---

## Repository Structure

```
Inter-VLAN-Routing/
├── Inter-VLAN-Routing.pkt     # Packet Tracer project file
└── README.md
```

---

## How to Run

1. Install [Cisco Packet Tracer](https://www.netacad.com/courses/packet-tracer).
2. Download or clone this repository.
3. Open `Inter-VLAN-Routing.pkt` in Packet Tracer.
4. Open the switch CLI and run `show vlan brief` and `show interfaces trunk`.
5. Open the router CLI and run `show ip interface brief`.
6. Use the Laptop Command Prompt to ping devices in other VLANs.

---

## Skills Demonstrated

- VLAN Creation and Naming
- Access and Trunk Port Configuration
- 802.1Q Encapsulation
- Inter-VLAN Routing (Router-on-a-Stick)
- Router Sub-interfaces
- Cisco IOS CLI
- ICMP Ping and Network Troubleshooting
- Network Documentation

---

## Author

<table>
  <tr>
    <td align="center">
      <a href="https://github.com/mohamedmousa2003">
        <img src="https://github.com/mohamedmousa2003.png" width="90" alt="Mohamed Moussa"/><br/>
        <sub><b>Mohamed Moussa</b></sub>
      </a>
    </td>
  </tr>
</table>
