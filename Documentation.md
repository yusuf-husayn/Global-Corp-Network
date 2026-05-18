---
title: Network Configuration, Routing, and Security Audit Master Note
tags:
  - study
  - exam
  - networking
  - security
  - configuration
date: 2026-05-13T06:43:00
status: studying
format: obsidian-md
---
## Network Topology and IP Allocation

### Detailed Explanation

The network is divided into several main sections connecting branches together via the HQ. The main center of the network is the HQ, which consists of 2 Routers operating in an Active and Standby setup, specifically `R1` and `R2`. The HQ is connected to an ISP represented by a router named `R5`, and this ISP distributes IPs to the HQ and acts as the Default Route for the network.

There are two primary branches connected to the HQ:

1) The first branch is Branch A for Finance, represented by router `R3`. This branch connects to `R1` and uses EIGRP 100 as its Routing Protocol. This branch contains devices such as `PC1` with the IP `192.168.3.10` and `PC3` with the IP `192.168.3.11`, and their Default Gateway is `192.168.3.1`.

2) The second branch is Branch B for Sales, represented by router `R4`. This branch connects to `R2` and uses RIPv2 as its Routing Protocol. This branch contains `PC2` with the IP `192.168.4.10`, and its Gateway is `192.168.4.1`.

In the HQ, router `R2` is connected to a Server Farm containing a Server with the IP `192.168.20.100`, and its Gateway is `192.168.20.1`. There is also an Admin PC with the IP `192.168.20.10`. The Link between `R1` and `R2` runs the HSRP protocol to ensure Redundancy.

> [!NOTE]
> The Topology relies on the concept of High Availability in the HQ using HSRP, and it utilizes Multiple Routing Protocols to connect the different branches.

### Key Points Summary

- The network consists of an ISP, an HQ, and 2 Branches.
- The HQ contains `R1` (Active) and `R2` (Standby).
- Branch A (Finance) uses EIGRP 100.
- Branch B (Sales) uses RIPv2.
- The Server Farm is located behind `R2` in the HQ.

> [!TIP]
> Always link each branch with its specific Routing Protocol and the router it connects to. R3 pairs with R1 via EIGRP, and R4 pairs with R2 via RIPv2.

### Definitions

- **HQ (Headquarters)**: The main center of the network that connects the branches to the Internet and the Server Farm.
- **ISP**: The Internet Service Provider that supplies the network with the Default Route and external IPs.
- **Server Farm**: A dedicated area in the network for servers that provide services to other devices.

### Rules / Laws / Principles

- **Subnetting Rule**
- All End-Users and Servers have a Subnet Mask of `255.255.255.0`, which means /24.
- Conditions: This means every internal network can host up to 254 devices.
- Exceptions: The Serial Links between routers use a different Subnet Mask for Point-to-Point connections.

### Formulas / Equations

Network Address = IP Address AND Subnet Mask

This formula determines the Network ID for any device. Example: `PC1` (`192.168.3.10`) with Mask (`255.255.255.0`) resides in the `192.168.3.0` network.

### Step-by-Step Process

1. Identify the location of the device within the Topology.
2. Determine the IP and Subnet Mask from the Table.
3. Identify the Default Gateway, which is the IP of the router Interface directly connected to the device.

### Examples

- **PC3 Configuration**: It takes the IP `192.168.3.11` and Mask `255.255.255.0`, communicating with `R3` as its Gateway on IP `192.168.3.1`.

### Common Mistakes / Traps

- Confusing the Default Gateway assignments for the devices.

> [!WARNING]
> Remember that the Gateway for the Server Farm is `192.168.20.1` and not a random IP; this is the `R2` Interface facing the servers.

### High-Risk Exam Content

- IP distribution and device connectivity.
- The specific types of Routing Protocols used in each branch.

### Understanding Checkpoints

- Define the role of R5 in this topology.
- Explain which routing protocol connects Branch A to HQ.
- Compare the Default Gateways of PC1 and PC2.

### Exam-Style Questions

>[!CAUTION]
>**Q1 :** What is the IP address and default gateway of the Server in the Server Farm?

>[!IMPORTANT]
>**Answer :** The IP is `192.168.20.100` and the default gateway is `192.168.20.1`.

>[!CAUTION]
>**Q2 :** Which routing protocol is running between HQ Active (R1) and Branch A Finance (R3)?

>[!IMPORTANT]
>**Answer :** EIGRP 100.

---
## ISP Configuration (R5)

### Detailed Explanation

The ISP is represented by a router named `R5`. This router performs two main functions. First, the `GigabitEthernet0/1` Interface has a Static IP of `203.0.113.1` with a Subnet Mask of `255.255.255.0`, and this is the Interface connected to the HQ (specifically `R1`).

Second, this router has an active DHCP Pool named `ISP_POOL`. The function of this Pool is to automatically distribute IPs for the `203.0.113.0` network. The DHCP is configured to give Clients the Default Router `203.0.113.1`, which is the exact same IP as the `G0/1` Interface on `R5`. This means the HQ will receive its IP and Gateway from this DHCP.

> [!NOTE]
> The ISP operates as a DHCP Server here to facilitate the IP Allocation process for the HQ's External Interface.

### Key Points Summary

- Router Name: ISP.
- The Interface connected to the HQ is `G0/1` and its IP is `203.0.113.1`.
- There is a DHCP Pool named `ISP_POOL` distributing the `203.0.113.0/24` network.
- The Default Router in the DHCP is set to `203.0.113.1`.

> [!TIP]
> Always remember that the `default-router` inside the DHCP Pool is the IP of the router itself that faces the Clients.

### Definitions

- **DHCP Pool**: A set of IPs and configurations that the DHCP Server distributes to Devices on the network.

### Rules / Laws / Principles

- **DHCP Configuration Rule**
- You must define a name for the Pool, followed by the Network from which IPs will be distributed, and the Default Router.
- Conditions: The Network and the Interface IP must be within the same Subnet.

### Formulas / Equations

```bash
ip dhcp pool <POOL_NAME>
 network <NETWORK_IP> <SUBNET_MASK>
 default-router <GATEWAY_IP>
````

This is the standard Syntax for creating a DHCP Pool as applied in `R5`.

### Step-by-Step Process

1. Enter Global Configuration Mode using `configure terminal`.

2. Change the router's name using `hostname ISP`.

3. Enter the Interface, assign an IP, and enable it using `no shutdown`.

4. Create the DHCP Pool and define the Network and Default Router.

5. Save configurations using `write memory`.


### Examples

- **DHCP Network**: `network 203.0.113.0 255.255.255.0` instructs the router to distribute IPs from this network.

### Common Mistakes / Traps

- Forgetting to execute `no shutdown` on the Interface.

> [!WARNING]
> 
> If you forget to execute `no shutdown`, the Interface will remain Administratively Down and the DHCP will not function at all.

### High-Risk Exam Content

- How to create a DHCP Pool.

- Interface configuration commands.

### Understanding Checkpoints

- Explain the purpose of `default-router 203.0.113.1` in the ISP configuration.
  
- Why is `GigabitEthernet0/1` assigned a static IP while it provides DHCP?.

### Exam-Style Questions

> [!CAUTION]
> **Q1 :** How does R1 obtain its public IP address from the ISP?

> [!IMPORTANT]
> **Answer :** It obtains it via DHCP, provided by the `ISP_POOL` configured on R5.

> [!CAUTION]
> 
> **Q2 :** What command is used to specify the default gateway inside the DHCP pool?

> [!IMPORTANT]
> 
> **Answer :** `default-router 203.0.113.1`.

---
## Headquarters Configuration (HQ - R1 & R2)

### Detailed Explanation

The HQ consists of two main routers functioning together as a single Gateway for internal devices using the HSRP (Hot Standby Router Protocol).

Router `R1` is the Active Router. The Interface facing the ISP (`G0/1`) is configured with `ip address dhcp` to obtain an IP automatically from router `R5`. The internal Interface (`G0/0`) has the IP `192.168.1.2`, and is configured with HSRP Group 1 using the Virtual IP `192.168.1.1`. `R1`'s Priority is set to `150`, and it has the `preempt` command applied, which forcefully makes it the primary Active router. `R1` connects to Branch A via `Serial0/0/0` with IP `10.1.1.1` and Subnet Mask `255.255.255.252`, configured with a `clock rate 64000` because it acts as the DCE. `R1` runs OSPF Area 0 for the internal network, registering the `192.168.1.0` network, and runs EIGRP 100 for the `10.1.1.0` network to communicate with the branch. Furthermore, it has 2 Static Routes: one to reach the `192.168.4.0` network (Branch B) via `R2` (`192.168.1.3`), and a Default Route that directs all external traffic to the ISP at `203.0.113.1`.

Router `R2` is the Standby Router. It has ACL 102 applied to it, which `deny`s any TCP traffic, specifically port `80` (HTTP), from traveling from `PC1` (`192.168.3.10`) to the Server (`192.168.20.100`), while executing a `permit` for everything else. Interface `G0/0` connects to the HQ, has the IP `192.168.1.3`, participates in HSRP Group 1 with the same Virtual IP `192.168.1.1`, and uses `preempt` but without a defined Priority, so it assumes the Default (100) and becomes Standby. Interface `G0/1` connects to the Server Farm, has the IP `192.168.20.1`, and ACL 102 is applied to it in the `out` direction. It connects to Branch B via `Serial0/0/0` with IP `10.2.2.1` and `clock rate 64000`. `R2` runs OSPF for two networks (`192.168.1.0` and `192.168.20.0`) and runs RIP Version 2 for the `10.0.0.0` network with Auto-Summary disabled. It has a Static Route directing traffic to Branch A (`192.168.3.0`) via `R1` (`192.168.1.2`).

> [!IMPORTANT]
> The `preempt` command in HSRP allows the router with the higher Priority to immediately reclaim the Active role if it recovers from a failure.

### Key Points Summary

- `R1` is the Active HSRP with a Priority of `150`.

- `R2` is the Standby HSRP taking the Default Priority.

- The Virtual IP for HSRP is `192.168.1.1`.

- `R1` runs OSPF 1 and EIGRP 100.

- `R2` runs OSPF 1 and RIPv2.

- `R2` contains ACL 102, which protects the Server Farm from HTTP traffic originating from `PC1`.

- Serial Interfaces require `clock rate 64000`.

> [!NOTE]
> The ACL 102 on `R2` complements the function of ACL 101 on `R3` to achieve complete Network Segmentation.

### Definitions

- **HSRP (Hot Standby Router Protocol)**: A protocol that creates a Virtual Router with a single IP to act as a backup Gateway in case the primary router fails.

- **Clock Rate**: The data transfer speed configured on the DCE side of a Serial Link.

- **Auto-Summary**: A routing protocol feature that aggregates Subnets into a Major Network, which must be disabled using `no auto-summary` to transmit the correct Subnet Mask.


### Rules / Laws / Principles

- **HSRP Election Rule**

- The router possessing the higher Priority becomes the Active router.

- Conditions: If Priorities are equal, the router with the highest IP address wins.

- **ACL Application Rule**

- An Extended ACL should be placed as close to the Source as possible, but here it is placed on `R2` `out` to protect the Server Farm directly.

### Formulas / Equations

```bash
standby <group_number> ip <virtual_ip>
standby <group_number> priority <value>
standby <group_number> preempt
```

These are the primary steps to enable HSRP and control which router is Active and which is Standby.

### Step-by-Step Process

1. Configure the Interfaces with appropriate IPs.

2. Enable HSRP using a shared Virtual IP.

3. Assign a higher Priority to the router intended to be Active and enable Preempt.

4. Enable the required Routing Protocols (OSPF, EIGRP, RIP).

5. Write the Static Routes to connect segments not running the same protocol.

### Examples

- **Static Route on R1**: `ip route 192.168.4.0 255.255.255.0 192.168.1.3` means to reach the `192.168.4.0` network (Branch B), forward the traffic to the IP of `R2` (`192.168.1.3`).

### Common Mistakes / Traps

- Forgetting to include the Subnet Mask with the Static Route.
  
- Applying the ACL in the wrong direction (`in` instead of `out`, or vice versa).

> [!CAUTION]
> 
> If you do not write the `preempt` command on `R1`, when `R1` fails and recovers, `R2` will remain Active and `R1` will not reclaim its primary role.

### High-Risk Exam Content

- HSRP configurations dictating who is Active and who is Standby and why.

- The Syntax for the Extended ACL.

- Default Route commands like `ip route 0.0.0.0 0.0.0.0`.


### Understanding Checkpoints

- Explain why R1 uses `ip address dhcp` on GigabitEthernet0/1.

- Define what `deny tcp host 192.168.3.10 host 192.168.20.100 eq 80` does.

- Compare the routing protocols running on R1 and R2.

### Exam-Style Questions

>[!CAUTION]
>**Q1 :** Which interface on R2 has ACL 102 applied to it, and in which direction?

>[!IMPORTANT]
>**Answer :** GigabitEthernet0/1, applied in the `out` direction.

>[!CAUTION]
>**Q2 :** What makes R1 the Active HSRP router?

>[!IMPORTANT]
>**Answer :** R1 is configured with `standby 1 priority 150`, which is higher than the default priority of 100 on R2.

---
## Branches Configuration (Branch A - R3 & Branch B - R4)

### Detailed Explanation

Router `R3` represents Branch A (Finance). It has ACL 101, which executes a `deny` for any IP traffic coming from the `192.168.4.0` network (Sales) destined for a specific device, `192.168.3.10` (`PC1`), and then executes a `permit` for the rest of the traffic. The internal Interface `G0/0` has the IP `192.168.3.1`, and ACL 101 is applied to it in the `out` direction. The external Interface `Serial0/0/0` has the IP `10.1.1.2` (connected to `R1`). `R3` runs EIGRP 100 for its networks (`10.1.1.0` and `192.168.3.0`). To reach any network it lacks, it utilizes a Default Route `ip route 0.0.0.0 0.0.0.0 10.1.1.1` to direct traffic to `R1`.

Router `R4` represents Branch B (Sales). The internal Interface `G0/0` has the IP `192.168.4.1`. The external Interface `Serial0/0/0` has the IP `10.2.2.2` (connected to `R2`). `R4` runs RIP Version 2 with `no auto-summary` for its networks (`10.0.0.0` and `192.168.4.0`). It also utilizes a Default Route `ip route 0.0.0.0 0.0.0.0 10.2.2.1` to direct traffic to `R2`.

> [!NOTE]
> 
> The Branches here use Default Routes to reach the HQ and the Internet, which reduces the Routing Table size on the branch routers since there is nothing else behind them.

### Key Points Summary

- `R3` uses EIGRP 100 and holds ACL 101.

- ACL 101 prevents the Sales network from communicating with `PC1` in Finance.

- `R4` uses RIP Version 2.

- Both branches utilize Static Default Routes pointing to the HQ.

> [!TIP]
> 
> When you see `0.0.0.255` in a network command inside OSPF, EIGRP, or ACL, this is called a Wildcard Mask, which is the inverse of a normal Subnet Mask.

### Definitions

- **Wildcard Mask**: The inverse of a Subnet Mask, used in Routing Protocol and ACL configurations to define network size (e.g., `/24` becomes `0.0.0.255`).

### Rules / Laws / Principles

- **ACL Implicit Deny Rule**

- At the end of every Access-list, there is a hidden line that executes a Deny All.

- Exception: Therefore, we must explicitly write `access-list 101 permit ip any any` at the end to allow the remaining traffic to pass.

### Formulas / Equations

```bash
access-list <number> deny/permit <protocol> <source> <destination>
```

This is the foundational rule for building an Extended ACL (ranging from 100 to 199), specifying the Source, Destination, and Protocol precisely.

### Step-by-Step Process

1. Configure the Interfaces and IPs for each branch.

2. Program the appropriate Routing Protocol for each branch (EIGRP for `R3`, and RIP for `R4`) and add their connected networks.

3. Add a Static Default Route pointing to the Next Hop in the HQ.

4. Program and apply the required ACLs for protection.

### Examples

- **EIGRP Network Command**: `network 192.168.3.0 0.0.0.255` adds this network to EIGRP and enables the Protocol on the Interface within this network.

### Common Mistakes / Traps

- Utilizing a standard Subnet Mask instead of a Wildcard Mask within the ACL or EIGRP.

> [!WARNING]
> 
> EIGRP requires you to enter the Autonomous System number (like `100` in this case), and this number must be identical across all routers intending to run EIGRP together; if it differs, they will not establish adjacency.

### High-Risk Exam Content

- The distinction between RIPv2 configurations and EIGRP configurations.

- Application of the Default Route.

### Understanding Checkpoints

- Explain the purpose of `no auto-summary` in RIP version 2.

- Define what `access-list 101 permit ip any any` does and why it is necessary.

### Exam-Style Questions

>[!CAUTION]
>**Q1 :** Which routing protocol is used by Branch B (Sales) to communicate with the rest of the network?

>[!IMPORTANT]
>**Answer :** RIP version 2.

>[!CAUTION]
>**Q2 :** What is the effect of the command `ip route 0.0.0.0 0.0.0.0 10.1.1.1` on R3?

>[!IMPORTANT]
>**Answer :** It creates a default route that sends all unknown traffic to R1 (HQ Active) via the next-hop IP 10.1.1.1.

---
## Device Hardening Script & Security Audit

### Detailed Explanation

The Device Hardening script consists of 5 essential steps. First, `service password-encryption` encrypts any passwords written in Plaintext within the configuration. Second, `enable secret Admin@123` creates a strong, encrypted password to access the Enable (Privileged EXEC Mode). Third, `banner motd` establishes a legal warning message shown to anyone attempting a Login, stating that unauthorized access is prohibited. Fourth, securing the Console by entering `line console 0`, setting the password `cisco`, enabling `login` to prompt for it, and applying `logging synchronous` to prevent system messages from interrupting the commands being typed. Fifth, securing Remote Access by entering `line vty 0 4`, setting the password `cisco`, and enabling `login`.

Following these configurations, a Security Audit and penetration test were conducted. Protection strategies included: Network Segmentation using ACLs to isolate Finance from Sales, Device Hardening using encrypted passwords and warning messages, and Service Restriction by blocking port `80` to the Server while permitting ICMP (Ping) for network diagnostics.

The Test Cases proved the success of the plan: `SEC-01` attempted to Ping from `PC2` to `PC1` and was blocked by `R3`'s ACL. `SEC-02` executed a Ping from `PC2` to `PC3` successfully. `SEC-03` attempted HTTP access from `PC1` to the Server and was blocked by `R2`'s ACL. `SEC-04` executed a Ping from `PC1` to the Server successfully.

> [!TIP]
> 
> The success of the Test Cases confirms that the implemented security policies are effective and properly isolate services and departments.

### Key Points Summary

- `service password-encryption` to encrypt all passwords.

- `enable secret` to create an encrypted password for Privileged mode.

- `banner motd` acts as a legal warning message.

- Securing Console and VTY (0 through 4) with the password `cisco`.

- The Security Audit tested 4 scenarios to verify that the ACLs function and block unauthorized Traffic.

> [!WARNING]
> Why use `enable secret` instead of `enable password`? Because `secret` encrypts the password using MD5 or stronger algorithms, whereas `password` leaves it in Plaintext if `service password-encryption` is not utilized.

### Definitions

- **MOTD (Message of the Day)**: A message appearing before Login, typically used for legal warnings.

- **VTY (Virtual Teletype)**: The virtual lines permitting remote connection to the router via Telnet or SSH.

- **Red Team**: A penetration testing team that attempts to breach the network to verify defense strength.

### Rules / Laws / Principles

- **Security Mitigation Rule**

- Every Infrastructure Device must be protected with a password, encrypted, and display a legal warning message.

### Formulas / Equations

```bash
line vty <start_number> <end_number>
 password <your_password>
 login
```

Securing remote connection lines.

### Step-by-Step Process

1. Enable global encryption using `service password-encryption`.

2. Set the secret Enable password.

3. Create the MOTD Banner using delimiters like `#`.

4. Secure the Console line and add `logging synchronous`.

5. Secure the VTY lines.

### Examples

- **Test Case SEC-03**: The source is PC1 in Finance attempting to reach the Server using HTTP on port 80; the result was a Request Timeout because this Traffic was blocked by ACL 102 on R2.

### Common Mistakes / Traps

- Forgetting to write the `login` command under `line console` or `line vty`, which results in the router not prompting for the password you configured.

> [!CAUTION] 
> If you omit `logging synchronous`, system messages will appear in the middle of words typed into the Console, corrupting the command line.

### High-Risk Exam Content

- Fundamental Hardening commands and their functions.

- Interpreting Ping results based on applied ACLs.

### Understanding Checkpoints

- Explain the difference between `enable secret` and `service password-encryption`.
  
- Why did Test SEC-02 pass while SEC-01 was blocked, even though both originate from PC2?

### Exam-Style Questions

>[!CAUTION]
>**Q1 :** Which command prevents console messages from interrupting the commands you are typing?

>[!IMPORTANT]
>**Answer :** `logging synchronous`.

>[!CAUTION]
>**Q2 :** What was the actual result of the SEC-01 test case?

>[!IMPORTANT]
>**Answer :** Blocked by R3 ACL.

---
# Extracted Rules & Laws

- **Subnetting Rule**: All user devices and servers utilize a /24 Subnet Mask.

- **HSRP Election Rule**: The router with the higher Priority becomes the Active Router, and `preempt` forces the router to reclaim its role.

- **ACL Implicit Deny Rule**: A hidden line exists at the end of every ACL that denies all connections unless explicitly permitted.

- **Security Mitigation Rule**: Password encryption and warning messages must be applied to all infrastructure devices.

# Extracted Formulas

- **HSRP Configuration**:
    `standby 1 ip 192.168.1.1`

    `standby 1 priority 150`

    `standby 1 preempt`.

- **DHCP Pool Configuration**:
    `ip dhcp pool <name>`

    `network <net> <mask>`

    `default-router <ip>`.

- **Extended ACL Configuration**:
    `access-list 102 deny tcp host 192.168.3.10 host 192.168.20.100 eq 80`.

---
# Study Notes

- The ISP provides IPs to the HQ via DHCP.

- The HQ relies on HSRP to ensure service continuity between R1 and R2.

- The Finance branch was isolated from Sales to prevent access to specific devices.

- Server access via HTTP protocol was blocked for specific devices, while Ping was permitted for diagnostics.

# Master Summary

- **Topology**: ISP -> HQ (R1/R2 HSRP) -> Branches (R3/R4) & Server Farm.

- **Protocols**: OSPF (HQ), EIGRP 100 (HQ/Branch A), RIPv2 (HQ/Branch B).

- **Security**: Extended ACLs on R2 (Out) and R3 (Out) for Segmentation.

- **Hardening**: Encrypted passwords, MOTD banner, Console/VTY security.

- **Testing**: Confirmed ACL functionality through Red Team simulation.

# Exam Notes

- Focus on which interface the ACL is applied to and in which direction (`out` in both cases here).

- Remember the exact IPs and subnets, especially the HSRP Virtual IP (`192.168.1.1`).

- Know command abbreviations: `conf t` = `configure terminal`, `int g0/0` = `interface GigabitEthernet0/0`, `wr` = `write memory`.

- Understand that `eq 80` in an ACL refers to HTTP traffic.

# Concept Connections

- **HSRP & Default Gateways**: HSRP creates a Virtual IP which acts as a Default Gateway for any internal device directly connected to the HQ.

- **ACLs & Network Segmentation**: ACLs implement Security Audit policies to block unauthorized Ping and HTTP traffic, ensuring the success of Test Cases.

- **DHCP & ISP Integration**: The `ip address dhcp` configuration on R1 directly connects to the `ISP_POOL` created on R5.

# Glossary

- **HSRP**: Hot Standby Router Protocol.

- **EIGRP**: Enhanced Interior Gateway Routing Protocol.

- **OSPF**: Open Shortest Path First.

- **RIPv2**: Routing Information Protocol version 2.

- **ACL**: Access Control List.

- **DHCP**: Dynamic Host Configuration Protocol.

- **MOTD**: Message of the Day.

- **VTY**: Virtual Teletype.

---