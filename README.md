# Cisco High Availability (HA) Core Network Lab

> A project designed and implemented in Cisco Packet Tracer to build a resilient, enterprise-grade core network that ensures maximum uptime and business continuity by mitigating common points of failure.

This lab demonstrates a practical application of the concepts required for the CCNA certification.
**You can download the Packet Tracer lab file [here](./lab-file/).**

## 🎯 Project Objectives
- **Mitigate Device Failure:** Deploy a redundant pair of Layer 3 core switches to prevent a single hardware failure from causing a network outage.
- **Ensure Gateway Resiliency:** Implement the Hot Standby Router Protocol (HSRP) to create a fault-tolerant virtual gateway for end devices.
- **Manage Link Failure & Loops:** Utilize Spanning Tree Protocol (STP) to manage redundant physical links, prevent network loops, and enable automatic path failover.
- **Enhance Bandwidth & Redundancy:** Aggregate multiple physical links into a single logical EtherChannel to increase inter-switch bandwidth and provide link-level fault tolerance.

## 🛠️ Technologies & Protocols
- **Hardware:** Cisco 3650 (Layer 3 Core), Cisco 2960 (Layer 2 Access)
- **Simulation Software:** Cisco Packet Tracer
- **Redundancy Protocols:** HSRP (Hot Standby Router Protocol), STP (Spanning Tree Protocol)
- **Link Aggregation:** EtherChannel (LACP)
- **Core Networking:** VLANs, Inter-VLAN Routing, Trunking (802.1Q)

## 🏗️ Network Design & Topology
The network features a dual-core design. The two core switches are linked via a 2Gbps EtherChannel. The access layer switch has redundant uplinks to each core switch, managed by STP.

![Network Topology](images/network-topology.png)

### IP & VLAN Schema
The network is segmented into two VLANs for access and management traffic. HSRP provides a virtual IP gateway for each VLAN.

| VLAN ID | Name         | Subnet          | HSRP Virtual IP | Core-SW1 IP  | Core-SW2 IP  |
|---------|--------------|-----------------|-----------------|--------------|--------------|
| 10      | MANAGEMENT   | `10.10.10.0/24` | `10.10.10.1`    | `10.10.10.2` | `10.10.10.3` |
| 20      | ACCESS       | `10.10.20.0/24` | `10.10.20.1`    | `10.10.20.2` | `10.10.20.3` |

## ⚙️ Configuration & Verification

### 1. HSRP for Gateway Redundancy
HSRP was configured to provide a virtual gateway. `Core-SW1` was set as the Active router with a higher priority (110), while `Core-SW2` acts as the Standby.

**Verification command:** `show standby brief` on `Core-SW1`.
![HSRP Verification](images/hsrp-verification_1.png)
![HSRP Verification](images/hsrp-verification_2.png)

### 2. STP for Loop Prevention
`Core-SW1` was configured as the Root Bridge. STP on the `Access-SW` intelligently blocked the redundant path to `Core-SW2` to maintain a loop-free topology, keeping it on standby for failover.

**Verification command:** `show spanning-tree vlan 20` on `Access-SW`.
![STP Verification](images/stp-verification.png)

## 💥 Failure Simulation & High Availability Test
To validate the HA design, a failure of the primary switch (`Core-SW1`) was simulated by powering it down while running a continuous ping from the Server to its virtual gateway.

- **Test:** `ping 10.10.20.1 -t` from Server.
- **Action:** Power down `Core-SW1`.

### Test Results: Seamless Failover
The network automatically recovered with minimal service interruption.
1.  **HSRP Failover:** `Core-SW2` detected the failure and transitioned from Standby to the Active role.
2.  **STP Convergence:** The `Access-SW` unblocked its uplink port to `Core-SW2`, re-establishing a valid traffic path.
3.  **Connectivity Restored:** Network connectivity was restored after only a few dropped packets, demonstrating the effectiveness of the resilient design.

![Failover Ping Test](images/failover-test.png)

## 💡 Key Learnings
This project provided deep, hands-on experience in designing and implementing fault-tolerant networks. I gained practical skills in configuring and verifying mission-critical redundancy protocols (HSRP, STP) that are essential for maintaining business continuity. This lab proved my ability to translate network theory into a functional, resilient infrastructure.
