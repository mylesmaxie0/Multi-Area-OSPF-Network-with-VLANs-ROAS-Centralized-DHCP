# Multi-Area OSPF Network with VLANs ROAS Centralized DHCP

## **Overview**

This project is an enterprise networking lab built in **EVE-NG** using Cisco IOS images. The lab is designed to simulate a real-world enterprise network by integrating **Multi-Area OSPF**, **VLAN segmentation**, **Layer 3 switching**, **Router-on-a-Stick (ROAS)**, and a **Centralized DHCP Server**.

The objective is to demonstrate scalable network design, dynamic routing, and efficient network segmentation while providing hands-on experience with core enterprise networking technologies. The lab emphasizes best practices for routing, switching, and IP address management in a multi-area environment.

---

## **Objectives**

- Design a scalable enterprise network using Cisco technologies.
- Configure Multi-Area OSPF across three routing areas.
- Implement VLAN segmentation for multiple departments.
- Configure inter-VLAN routing using both Layer 3 switching and Router-on-a-Stick (ROAS).
- Deploy a centralized DHCP server to dynamically assign IP addresses across all VLANs.
- Verify end-to-end connectivity between all network segments.

---

## **Technologies Used**

- EVE-NG
- Cisco IOS
- Multi-Area OSPF
- Cisco Multilayer Switches
- Layer 2 Switching
- Layer 3 Switching (SVIs)
- Router-on-a-Stick (ROAS)
- IEEE 802.1Q Trunking
- Centralized DHCP
- DHCP Relay (`ip helper-address`)
- IPv4 Networking

---

## **Network Architecture**

The lab consists of:

- Three OSPF routing areas
- Two Cisco Multilayer Switches
- Multiple Cisco routers interconnected using OSPF
- VLAN segmentation for departmental networks
- A centralized DHCP server providing IP address assignments across all VLANs

The network uses a **hybrid inter-VLAN routing design**:

- **VLANs 10–40** are routed using **Switch Virtual Interfaces (SVIs)** on the Cisco multilayer switches, demonstrating Layer 3 switching within the campus network.
- **VLANs 50–60** are routed using **Router-on-a-Stick (ROAS)** with IEEE 802.1Q trunking, demonstrating traditional inter-VLAN routing through a Cisco router.

This hybrid architecture showcases multiple enterprise routing methods within a single environment while integrating dynamic routing through Multi-Area OSPF and centralized DHCP services.

---

## **VLAN Addressing Scheme**

| **VLAN** | **Network** | **Default Gateway** | **Description** |
| --- | --- | --- | --- |
| 10 | 192.168.10.0/24 | 192.168.10.1 | Network 1 – Department A |
| 20 | 192.168.20.0/24 | 192.168.20.1 | Network 1 – Department B |
| 30 | 192.168.30.0/24 | 192.168.30.1 | Network 2 – Department C |
| 40 | 192.168.40.0/24 | 192.168.40.1 | Network 2 – Department D |
| 50 | 192.168.50.0/24 | 192.168.50.1 | Network 3 – Department E (ROAS) |
| 60 | 192.168.60.0/24 | 192.168.60.1 | Network 3 – Department F (ROAS) |

---

## **Features Implemented**

- Multi-Area OSPF (Areas 0, 1, and 2)
- VLAN creation and segmentation
- Layer 3 Switching using SVIs
- Router-on-a-Stick (ROAS)
- IEEE 802.1Q trunking
- Dynamic routing with OSPF
- Centralized DHCP services
- DHCP relay using `ip helper-address`
- End-to-end Layer 3 connectivity
- Enterprise IP addressing design

---

## **Verification**

The following tests are used to validate the network configuration:

- OSPF neighbor adjacencies established successfully
- Dynamic routes learned and installed correctly
- VLAN communication verified
- Inter-VLAN routing functioning across both SVIs and ROAS
- DHCP leases assigned successfully to all VLANs
- Successful ping and traceroute verification between all VLANs and OSPF areas

---

## **Skills Demonstrated**

- Enterprise Network Design
- Cisco IOS Configuration
- Multi-Area OSPF Deployment
- VLAN Design and Segmentation
- Layer 2 and Layer 3 Switching
- Router-on-a-Stick Configuration
- DHCP and DHCP Relay Configuration
- IEEE 802.1Q Trunking
- Routing and Switching Troubleshooting
- Network Verification and Connectivity Testing

---

## Network Topology

<img width="1709" height="814" alt="Screenshot_2026-07-20_at_11 46 39_AM" src="https://github.com/user-attachments/assets/0601e426-cd72-4cee-934f-fdad78cf2e12" />


# Router 1 Configuration Summary

R1 acts as the **OSPF Area 0 backbone router** and provides **Router-on-a-Stick (ROAS) routing** for VLANs 50 and 60. It also connects to the centralized DHCP server and advertises networks between OSPF areas.

---

### Key Configurations

#### Router-on-a-Stick (ROAS)

```
interface Ethernet0/0.50
 encapsulation dot1Q 50
 ip address 192.168.50.1 255.255.255.0

interface Ethernet0/0.60
 encapsulation dot1Q 60
 ip address 192.168.60.1 255.255.255.0
```

- Configures VLAN gateways using 802.1Q subinterfaces.
- Provides Layer 3 routing for VLAN 50 and VLAN 60.

#### DHCP Relay

```
ip helper-address 172.168.10.2
```

- Forwards DHCP requests from client VLANs to the centralized DHCP server.

#### OSPF Configuration

```
router ospf 1
 router-id 1.1.1.1
```

- Enables OSPF routing and assigns a unique router ID.

```
network 172.168.10.0 0.0.0.3 area 0
network 172.168.11.0 0.0.0.3 area 0
network 172.168.12.0 0.0.0.3 area 0
network 192.168.50.0 0.0.0.255 area 2
network 192.168.60.0 0.0.0.255 area 2
```

- Advertises backbone links and VLAN networks into the appropriate OSPF areas.

#### Routed Connections

```
interface Ethernet0/1
 ip address 172.168.10.1 255.255.255.252
```

- Provides the routed connection to the centralized DHCP server.

```
interface Serial2/0
 ip address 172.168.11.1 255.255.255.252

interface Serial2/1
 ip address 172.168.12.1 255.255.255.252
```

- Provides OSPF point-to-point connections to neighboring routers.

### Summary

R1 provides:

- OSPF Area 0 backbone routing
- Multi-area OSPF connectivity
- ROAS inter-VLAN routing for VLANs 50 and 60
- DHCP relay functionality
- Connectivity between routers and centralized network services

---

# R2 Configuration Summary

R2 functions as an **OSPF Area 0 and Area 1 router**, providing connectivity between the OSPF backbone and MLS1. R2 acts as a transit router, forwarding traffic between network segments.

---

### Key Configurations

#### Routed Link to MLS1

```
interface Ethernet0/0
 description ## Link to MLS1 ##
 ip address 172.168.14.1 255.255.255.252
```

- Provides a routed point-to-point connection between R2 and MLS1.
- Advertised into OSPF Area 1.

---

#### Routed Link to R1

```
interface Serial2/0
 description ## Link to Router 1 ##
 ip address 172.168.11.2 255.255.255.252
```

- Provides an OSPF point-to-point connection to R1.
- Operates within the OSPF backbone (Area 0).

---

#### OSPF Configuration

```
router ospf 1
 router-id 2.2.2.2
```

- Enables OSPF process 1.
- Assigns a unique router ID for OSPF identification.

```
network 172.168.11.0 0.0.0.3 area 0
network 172.168.14.0 0.0.0.3 area 1
```

- Advertises R2’s routed links into the appropriate OSPF areas.
- Maintains connectivity between Area 0 and Area 1.

---

#### Summary

R2 provides:

- OSPF routing between Area 0 and Area 1
- Connectivity between R1 and MLS1
- Point-to-point routed links using /30 networks
- Dynamic route exchange through OSPF

---

# R3 Configuration Summary

R3 functions as an **OSPF Area 0 and Area 3 router**, providing connectivity between the OSPF backbone and MLS2. It serves as a transit router, forwarding traffic between Area 3 and the rest of the network.

---

### Key Configurations

#### **Routed Link to MLS2**

```
interface Ethernet0/0
 description ## Link to MLS2 ##
 ip address 172.168.13.1 255.255.255.252
```

- Provides a routed point-to-point connection between R3 and MLS2.
- Advertised into OSPF Area 3.

---

#### **Routed Link to R1**

```
interface Serial2/1
 description ## Link to Router 1 ##
 ip address 172.168.12.2 255.255.255.252
```

- Provides an OSPF point-to-point connection to R1.
- Operates within the OSPF backbone (Area 0).

---

#### **OSPF Configuration**

```
router ospf 1
 router-id 3.3.3.3
```

- Enables OSPF process 1.
- Assigns a unique router ID for OSPF identification.

```
network 172.168.12.0 0.0.0.3 area 0
network 172.168.13.0 0.0.0.3 area 3
```

- Advertises R3’s routed links into the appropriate OSPF areas.
- Maintains connectivity between Area 0 and Area 3.

---

#### **Summary**

R3 provides:

- OSPF routing between Area 0 and Area 3
- Connectivity between R1 and MLS2
- Point-to-point routed links using /30 networks
- Dynamic route exchange through OSPF

---

# MLS1 **Configuration Summary**

MLS1 functions as a **Layer 3 switch for OSPF Area 1**, providing inter-VLAN routing for VLANs 10 and 20. It serves as the default gateway for connected devices, relays DHCP requests to the centralized DHCP server, and exchanges routes with R2 using OSPF.

---

### **Key Configurations**

#### **Enable Layer 3 Routing**

```
ip routing
```

- Enables the multilayer switch to perform Layer 3 routing between VLANs.
- Allows SVIs to act as default gateways for end devices.

---

#### **Routed Link to R2**

```
interface Ethernet0/0
 no switchport
 ip address 172.168.14.2 255.255.255.252
```

- Converts the interface into a routed port.
- Provides a point-to-point connection to R2 for OSPF routing.

---

#### **Access Ports**

```
interface Ethernet0/1
 switchport access vlan 10

interface Ethernet0/3
 switchport access vlan 20
```

- Assigns end devices to VLAN 10 and VLAN 20.
- Provides Layer 2 connectivity for hosts.

---

#### **VLAN Interfaces (SVIs)**

```
interface Vlan10
 ip address 192.168.10.1 255.255.255.0
 ip helper-address 172.168.10.2
```

- Creates the default gateway for VLAN 10.
- Relays DHCP requests to the centralized DHCP server.

```
interface Vlan20
 ip address 192.168.20.1 255.255.255.0
 ip helper-address 172.168.10.2
```

- Creates the default gateway for VLAN 20.
- Relays DHCP requests to the centralized DHCP server.

---

#### **OSPF Configuration**

```
router ospf 1
 router-id 4.4.4.4
```

- Enables OSPF process 1.
- Assigns a unique router ID.

```
network 172.168.14.0 0.0.0.3 area 1
network 192.168.10.0 0.0.0.255 area 1
network 192.168.20.0 0.0.0.255 area 1
```

- Advertises the routed uplink and VLAN networks into OSPF Area 1.
- Allows the rest of the network to reach VLANs 10 and 20 dynamically.

---

#### **Spanning Tree**

```
spanning-tree mode pvst
```

- Enables Per-VLAN Spanning Tree (PVST).
- Prevents Layer 2 switching loops and provides loop-free redundancy.

---

#### **Summary**

MLS1 provides:

- Layer 3 switching for VLANs 10 and 20
- Inter-VLAN routing using Switch Virtual Interfaces (SVIs)
- Centralized DHCP relay for client devices
- OSPF Area 1 connectivity with R2
- Layer 2 access ports for end-user devices
- PVST for loop prevention and network stability

---

# **MLS2 Configuration Summary**

MLS2 functions as a **Layer 3 switch for OSPF Area 3**, providing inter-VLAN routing for VLANs 30 and 40. It serves as the default gateway for connected devices, relays DHCP requests to the centralized DHCP server, and exchanges routes with R3 using OSPF.

---

### **Key Configurations**

#### **Enable Layer 3 Routing**

```
ip routing
```

- Enables the multilayer switch to perform Layer 3 routing between VLANs.
- Allows SVIs to act as default gateways for end devices.

---

#### **Routed Link to R3**

```
interface Ethernet0/0
 no switchport
 ip address 172.168.13.2 255.255.255.252
```

- Converts the interface into a routed port.
- Provides a point-to-point connection to R3 for OSPF routing.

---

#### Access Ports

```
interface Ethernet0/1
 switchport access vlan 30

interface Ethernet0/3
 switchport access vlan 40
```

- Assigns end devices to VLAN 30 and VLAN 40.
- Provides Layer 2 connectivity for client devices.

---

#### **VLAN Interfaces (SVIs)**

```
interface Vlan30
 ip address 192.168.30.1 255.255.255.0
 ip helper-address 172.168.10.2
```

- Creates the default gateway for VLAN 30.
- Relays DHCP requests to the centralized DHCP server.

```
interface Vlan40
 ip address 192.168.40.1 255.255.255.0
 ip helper-address 172.168.10.2
```

- Creates the default gateway for VLAN 40.
- Relays DHCP requests to the centralized DHCP server.

---

#### **OSPF Configuration**

```
router ospf 1
 router-id 5.5.5.5
```

- Enables OSPF process 1.
- Assigns a unique router ID.

```
network 172.168.13.0 0.0.0.3 area 3
network 192.168.30.0 0.0.0.255 area 3
network 192.168.40.0 0.0.0.255 area 3
```

- Advertises the routed uplink and VLAN networks into OSPF Area 3.
- Allows the rest of the network to dynamically learn routes to VLANs 30 and 40.

---

#### Spanning Tree

```
spanning-tree mode pvst
```

- Enables Per-VLAN Spanning Tree (PVST).
- Prevents Layer 2 loops and maintains a stable switching topology.

---

#### Summary

MLS2 provides:

- Layer 3 switching for VLANs 30 and 40
- Inter-VLAN routing using Switch Virtual Interfaces (SVIs)
- Centralized DHCP relay for client devices
- OSPF Area 3 connectivity with R3
- Layer 2 access ports for end-user devices
- PVST for loop prevention and network stability

---

# **DHCP-SERVER Configuration Summary**

The DHCP-SERVER provides **centralized DHCP services** for all VLANs in the network. It assigns IP addresses dynamically to clients across multiple OSPF areas, with DHCP requests relayed by the VLAN gateways using `ip helper-address`.

---

### Key Configurations

#### DHCP Excluded Addresses

```
ip dhcp excluded-address 192.168.10.1
ip dhcp excluded-address 192.168.20.1
ip dhcp excluded-address 192.168.30.1
ip dhcp excluded-address 192.168.40.1
ip dhcp excluded-address 192.168.50.1
ip dhcp excluded-address 192.168.60.1
```

- Reserves each VLAN’s default gateway address.
- Prevents DHCP from assigning gateway IPs to clients.

---

#### DHCP Pools

```
ip dhcp pool VLAN_10
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
```

(Similar configuration for VLANs 20, 30, 40, 50, and 60.)

- Creates a DHCP scope for each VLAN.
- Defines the subnet and default gateway clients receive when requesting an IP address.

---

#### Routed Connection

```
interface Ethernet0/0
 ip address 172.168.10.2 255.255.255.252
```

- Provides a routed point-to-point connection to R1.
- Allows DHCP requests relayed from across the network to reach the DHCP server.

---

#### OSPF Configuration

```
router ospf 1
 router-id 6.6.6.6
```

- Enables OSPF process 1.
- Assigns a unique router ID.

```
network 172.168.10.0 0.0.0.3 area 0
```

- Advertises the DHCP server’s routed link into OSPF Area 0.
- Allows all routers and multilayer switches to reach the DHCP server through dynamic routing.

---

#### Summary

The DHCP-SERVER provides:

- Centralized DHCP services for VLANs 10, 20, 30, 40, 50, and 60
- Dynamic IP address assignment using dedicated DHCP pools
- Reserved gateway addresses with DHCP exclusions
- OSPF connectivity for reachability across all network areas
- Support for DHCP relay (`ip helper-address`) from routers and multilayer switches

---

# **SW1 Configuration Summary**

SW1 functions as a **Layer 2 access switch**, providing connectivity for devices in VLANs 50 and 60. It connects to R1 using an **802.1Q trunk**, enabling Router-on-a-Stick (ROAS) for inter-VLAN routing.

---

### Key Configurations

#### Trunk Link to R1

```
interface Ethernet0/0
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 99
 switchport trunk allowed vlan 50,60
 switchport mode trunk
```

- Configures an 802.1Q trunk to R1.
- Allows VLANs 50 and 60 to traverse the trunk.
- Uses VLAN 99 as the native (untagged) VLAN.
- Enables Router-on-a-Stick communication between the switch and R1.

---

#### Access Ports

```
interface Ethernet0/1
 switchport access vlan 50

interface Ethernet0/2
 switchport access vlan 50

interface Ethernet0/3
 switchport access vlan 60

interface Ethernet1/0
 switchport access vlan 60
```

- Assigns end devices to VLAN 50 and VLAN 60.
- Provides Layer 2 connectivity for client devices.

---

#### Summary

SW1 provides:

- Layer 2 connectivity for VLANs 50 and 60
- 802.1Q trunk connection to R1
- Support for Router-on-a-Stick (ROAS)
- Access ports for end-user devices

---

# DHCP Verification

### PC3 - VLAN 20

<img width="497" height="315" alt="Screenshot_2026-07-21_at_1 29 26_PM" src="https://github.com/user-attachments/assets/7ea8498d-6d69-4aa5-b6db-a280e6f127ca" />


PC3 successfully obtained a DHCP lease from the centralized DHCP server using the **DORA (Discover, Offer, Request, Acknowledge)** process.

**Assigned Network Information:**

- **IP Address:** `192.168.20.2/24`
- **Default Gateway:** `192.168.20.1`
- **DHCP Server:** `172.168.10.2`
- **Lease Status:** Successfully assigned and active

This confirms that **DHCP relay (`ip helper-address`)**, **centralized DHCP**, and **VLAN 20 connectivity** are functioning correctly.

### PC7 - VLAN 60

<img width="394" height="244" alt="Screenshot_2026-07-21_at_1 31 49_PM" src="https://github.com/user-attachments/assets/d62beeec-7745-4ebb-babc-0232491e91e2" />


PC7 successfully obtained a DHCP lease from the centralized DHCP server using the **DORA (Discover, Offer, Request, Acknowledge)** process.

**Assigned Network Information:**

- **IP Address:** `192.168.60.2/24`
- **Default Gateway:** `192.168.60.1`
- **DHCP Server:** `172.168.10.2`
- **Lease Status:** Successfully assigned and active

This confirms that **DHCP relay (`ip helper-address`)**, **centralized DHCP**, and **Router-on-a-Stick (ROAS)** for VLAN 60 are functioning correctly.

---

# **End-to-End Connectivity Verification**

<img width="542" height="133" alt="Screenshot_2026-07-21_at_1 34 11_PM" src="https://github.com/user-attachments/assets/b8c663e9-ebc0-4313-b54e-190fa7d69296" />


PC3 (VLAN 20) successfully communicated with PC7 (VLAN 60) using ICMP.

**Test Results:**

- **Source Host:** PC3 (`192.168.20.2`)
- **Destination Host:** PC7 (`192.168.60.2`)
- **Result:** 5/5 successful replies
- **Average Latency:** ~10 ms
- **TTL:** 61

This successful ping verifies:

- Inter-VLAN routing between VLAN 20 and VLAN 60
- Router-on-a-Stick (ROAS) routing for VLAN 60
- Multi-Area OSPF route propagation across the network
- End-to-end connectivity between devices in different network segments

---

#### **Troubleshooting & Resolutions**

During the implementation of this lab, several issues were identified and resolved, demonstrating a systematic approach to network troubleshooting.

- **DHCP clients unable to obtain IP addresses:** Verified end-to-end connectivity, configured `ip helper-address` on VLAN gateway interfaces, and confirmed DHCP pools and default gateways on the centralized DHCP server.
- **Incorrect subnet masks on SVIs:** VLAN 30 and VLAN 40 were mistakenly configured with a `/30` subnet mask instead of `/24`, preventing proper client addressing. Updating the SVIs to `/24` resolved the issue.
- **Multiple DHCP helper addresses:** Removed outdated `ip helper-address` entries to ensure DHCP requests were forwarded only to the active DHCP server.
- **DHCP server reachability:** Verified OSPF advertisements and routing tables to ensure the DHCP server was reachable from all VLANs and routing areas.
- **Inter-VLAN connectivity validation:** Performed end-to-end ping tests between hosts in different VLANs to confirm successful routing across multilayer switches, Router-on-a-Stick, and the multi-area OSPF topology.
- **Configuration verification:** Used Cisco verification commands such as `show ip route`, `show ip ospf neighbor`, `show ip interface brief`, `show ip dhcp binding`, and client DHCP status to validate routing, address assignment, and network connectivity.

These troubleshooting steps resulted in a fully functional network with **dynamic routing, centralized DHCP, hybrid inter-VLAN routing (SVIs and ROAS), and successful end-to-end communication across all OSPF areas**.
