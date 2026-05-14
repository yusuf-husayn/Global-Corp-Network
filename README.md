# 🌐 Global Corp Network Simulation

A comprehensive enterprise network topology simulated in **Cisco Packet Tracer**. This project demonstrates core network engineering concepts including multi-protocol routing, high availability, lateral movement restriction, and secure infrastructure design.

---

## ✨ Key Features

* **Dynamic Routing Integration:** Implementation of OSPF (Area 0) for the core, EIGRP (AS 100) for Branch A (Finance), and RIPv2 for Branch B (Sales).
* **High Availability:** Core router redundancy using HSRP (Active/Standby) to maintain continuous uptime.
* **Security Policy & Hardening:** Extended Access Control Lists (ACLs) to restrict inter-departmental movement and protect the Server Farm, alongside strict device access control (MOTD, password encryption).
* **Automated Services:** IP allocation using a DHCP server deployed at the ISP edge.
* **Static Routing Connectivity:** Integration between different routing domains using Static Routes instead of automated redistribution.

---

## 🗺️ Network Topology
<img src="https://github.com/user-attachments/assets/2d44b88f-d965-47f6-a3e8-762aa39477c9" alt="Network Topology Diagram" style="max-width: 100%;">

---

## 📁 Project Files

| File Name | Description |
| :--- | :--- |
| `Global-Corp_Network.pkt` | The main Cisco Packet Tracer simulation file. |
| `Network Topology Diagram.jpg` | High-resolution visual representation of the complete enterprise network architecture. |
| `Commands.md` | Complete list of CLI commands used for device configuration and hardening. |
| `Documentation.md` | Detailed breakdown of the network topology, IP addressing, and protocol assignment. |
| `final project.pdf` | The original project requirements and grading rubric. |

---

## 🛠️ Tech Stack & Protocols

* **Simulation Environment:** Cisco Packet Tracer
* **Routing Protocols:** OSPF (Area 0), EIGRP (AS 100), RIPv2, Static Routing
* **Network Services:** DHCP, HSRP, ICMP
* **Security:** Standard/Extended ACLs, Device Hardening

---

## 🚀 How to Run

1. Install **Cisco Packet Tracer** on your machine.
2. Open the `Global-Corp_Network.pkt` file.
3. The network will initialize. Wait a few seconds for routing protocols (OSPF/EIGRP) to establish adjacencies.
4. Review `Commands.md` and `Documentation.md` to understand the configurations and test the defined security policies (e.g., executing verification test cases).

---

## 🧠 Core Architecture Highlights
* **Segmented Network Design:** Clear separation between the HQ Core, Finance Branch (Branch A), Sales Branch (Branch B), and the Server Farm.
* **Gateway Redundancy:** A Virtual IP (VIP) serves as the default gateway for the core network, failing over between R1 and R2 during failure events.
* **Red Teaming Verification:** Built-in test cases prove the network successfully blocks unauthorized Web (HTTP) access and inter-departmental ICMP traffic based on the applied ACLs.

---

## 👥 Contributors
* **[Yusuf Husayn](https://github.com/YourUsername)** - **[Yusuf Sayed](https://github.com/youssef-sayed8)** - **[Yusuf Ramadan](https://github.com/youseeframadan255)**


*Prepared for FCAI Beni-Suef University.*
