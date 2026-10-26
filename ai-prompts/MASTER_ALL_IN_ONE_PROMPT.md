# MASTER ALL-IN-ONE PROMPT: MSS Ltd Enterprise Network Implementation & Coursework Package

> **How to use this file:**  
> Copy the entire markdown block below (from `--- BEGIN PROMPT ---` to `--- END PROMPT ---`) and paste it directly into an AI model with a large context window (e.g. Claude 3.5 Sonnet, ChatGPT GPT-4o, or Gemini 1.5/2.0 Pro). It contains all instructions, IP plans, cabling rules, simulator constraints, and configuration deliverables needed to generate the full coursework solution in a single response.

---

```markdown
--- BEGIN PROMPT ---

# Project Assignment: Complete Implementation, Configuration, Verification & Vulnerability Report for MSS Ltd Campus Network

You are acting as a Principal Network Architect and Cisco Packet Tracer Senior Technical Specialist. Your objective is to design, document, configure, and verify the complete enterprise network for "MSS Ltd" according to rigorous academic university coursework specifications.

---

## 1. Company Profile, Capacity & Design Requirements

MSS Ltd occupies a 3-floor commercial building requiring an enterprise-grade campus network:
1. First Floor - Marketing Department:
   - 20 workstation hosts required.
   - Assigned Subnet: `192.168.10.0/27` (VLAN 10).
   - Switch: `SW-MKT` (Cisco Catalyst 2960-24TT).
2. Second Floor - Sales Department:
   - 25 workstation hosts required.
   - Assigned Subnet: `192.168.20.0/26` (VLAN 20).
   - Switches: Requires 2 switches due to 24-port physical switch limit:
     * `SW-SALES-A` (Cisco 2960-24TT, hosting Sales PCs 01-24).
     * `SW-SALES-B` (Cisco 2960-24TT, hosting Sales PC 25 and future capacity).
3. Third Floor - General Support Department:
   - 15 workstation hosts required.
   - Assigned Subnet: `192.168.30.0/27` (VLAN 30).
   - Switch: `SW-SUPPORT` (Cisco Catalyst 2960-24TT).
4. Central Server Room:
   - Switch: `SW-SERVER` (Cisco Catalyst 2960-24TT).
   - Production Services Subnet: `192.168.40.0/27` (VLAN 40)
     * `INFRA-SRV` (`192.168.40.10/27`): DHCP Server & DNS Server.
     * `MAIL-SRV` (`192.168.40.11/27`): Corporate E-mail Service.
     * `INTRANET-SRV` (`192.168.40.12/27`): Corporate Intranet Web Portal.
     * `MKT-DB-SRV` (`192.168.40.20/27`): Departmental Database Web Portal.
     * `SALES-DB-SRV` (`192.168.40.21/27`): Departmental Database Web Portal.
     * `SUPPORT-DB-SRV` (`192.168.40.22/27`): Departmental Database Web Portal.
   - Dedicated Management Subnet: `192.168.50.0/28` (VLAN 50)
     * `ADMIN-PC` (`192.168.50.10/28`): Secure administrative workstation.
     * Switch Management SVIs: `192.168.50.1` to `.15`.
   - Protected Backup & Archive Subnet: `192.168.60.0/28` (VLAN 60)
     * `BACKUP-DB-SRV` (`192.168.60.10/28`): Backup database server.
     * `ARCHIVE-SRV` (`192.168.60.11/28`): Archive server.
   - Security Blackhole Subnet: `VLAN 99` (`NATIVE-BLACKHOLE`) for unused switchports and trunk native isolation.
5. High-Availability Distribution & Core Layer:
   - Dual Multilayer Switches: `CORE-SW1` (Primary) and `CORE-SW2` (Secondary) using Cisco Catalyst 3560-24PS.
   - Redundancy Protocol: Hot Standby Router Protocol (HSRP).
     * `CORE-SW1`: HSRP Priority `110`, `preempt` enabled (Active Gateway for all SVIs).
     * `CORE-SW2`: HSRP Priority `100` (Standby Gateway for all SVIs).
   - Spanning Tree Protocol: Rapid-PVST+ (`spanning-tree mode rapid-pvst`).
     * `CORE-SW1`: `spanning-tree vlan 10,20,30,40,50,60,99 root primary`.
     * `CORE-SW2`: `spanning-tree vlan 10,20,30,40,50,60,99 root secondary`.
   - Core Interconnect: 802.1Q trunk on `GigabitEthernet0/1` carrying all VLANs with native VLAN 99.
   - Access Uplinks: Every access switch has dual 802.1Q uplinks (`Gi0/1` to `CORE-SW1`, `Gi0/2` to `CORE-SW2`).
6. Perimeter Security & Internet Boundary:
   - Stateful Firewall: Cisco ASA 5505 (`FW-ASA1`).
     * Inside Interface: `Vlan1`, Security Level 100, IP `10.255.0.1/29`.
     * Outside Interface: `Vlan2`, Security Level 0, IP `10.255.1.2/30`.
     * Stateful inspection enabled for ICMP, HTTP, and DNS under global policy.
     * Transit Routing: Static route pointing `192.168.0.0/16` back to HSRP virtual IP `10.255.0.4`.
   - Company Edge Router: Cisco 2911 (`EDGE-R1`).
     * `G0/0` (Inside): `10.255.1.1/30` (`ip nat inside`).
     * `G0/2` (Outside WAN): `203.0.113.2/30` (`ip nat outside`).
     * NAT Overload (PAT): Translates internal `192.168.0.0/16` out `G0/2`.
     * Default Route: `0.0.0.0 0.0.0.0 203.0.113.1` (towards ISP).
   - Simulated Internet Provider & Server:
     * `ISP-R1` (Cisco 2911): `G0/0` (`203.0.113.1/30`), `G0/1` (`198.51.100.1/24`).
     * `INTERNET-SRV`: `198.51.100.10/24` hosting public test web and DNS services.

---

## 2. Cisco Packet Tracer Simulator Constraints
You must strictly observe the following technical constraints in Cisco Packet Tracer:
- Cisco 3560 multilayer switches require `ip routing` to be explicitly executed in global configuration mode to enable L3 inter-VLAN forwarding.
- Trunk ports on the 3560 require `switchport trunk encapsulation dot1q` before `switchport mode trunk` can be entered.
- On the ASA 5505, IP addresses are assigned to VLAN interfaces (`interface Vlan1` and `interface Vlan2`), NOT to physical Ethernet ports. Physical ports are configured with `switchport access vlan X`.
- Database servers are represented using Packet Tracer's built-in HTTP server feature with customized HTML portal pages.

---

## 3. Required Outputs & Deliverables

Please provide a complete, beautifully formatted, publication-ready response containing the following sections:

### SECTION 1: MASTER VLAN & IP ADDRESSING TABLE
Provide an exhaustive table detailing:
- VLAN ID & Name
- Network Subnet (CIDR) & Subnet Mask (decimal)
- Usable Host Range & Broadcast Address
- Virtual HSRP Default Gateway IP
- CORE-SW1 Physical SVI IP (Priority 110)
- CORE-SW2 Physical SVI IP (Priority 100)
- Purpose & Allocation (DHCP pool range or static assignment)

### SECTION 2: PHYSICAL CABLING & PORT ALLOCATION MATRIX
Provide a complete markdown table listing every physical connection:
- Source Device & Source Port
- Target Device & Target Port
- Cable Type (Straight-Through, Crossover)
- Function (WAN, Transit, Core Trunk, Access Uplink, Endpoint)

### SECTION 3: TURNKEY CISCO IOS CONFIGURATION BLOCKS
Provide clean, pasteable CLI script blocks for every single device:
1. `CORE-SW1` (Host, L3 routing, VLANs, trunks, SVIs, HSRP with preempt, Rapid-PVST+, DHCP relay helper-addresses, default route to ASA).
2. `CORE-SW2` (Host, L3 routing, VLANs, trunks, SVIs, HSRP standby, Rapid-PVST+ secondary, DHCP relay, default route).
3. Access Switches (`SW-MKT`, `SW-SALES-A`, `SW-SALES-B`, `SW-SUPPORT`, `SW-SERVER`):
   - Dual trunk uplinks with native VLAN 99.
   - Access port ranges with `spanning-tree portfast` and `bpduguard enable`.
   - Management SVI on VLAN 50 with default gateway.
   - Unused ports disabled and isolated in VLAN 99.
4. `FW-ASA1` (Inside/Outside nameif, security levels, IP addressing, inspect policy for icmp/http/dns, static routes, access-list).
5. `EDGE-R1` (Interface IPs, NAT inside/outside, standard ACL 1, `ip nat inside source list 1 interface G0/2 overload`, static routes).
6. `ISP-R1` (Interface IPs and route forwarding).

### SECTION 4: SERVER SERVICES & CLIENT ENDPOINTS SETUP
- Detailed configuration parameters for `INFRA-SRV`:
  * 3x DHCP Scopes (Marketing, Sales, Support) with start IP, subnet mask, gateway, and DNS.
  * DNS A-records for intranet, email, and 3x departmental DB portals.
- Setup steps for `INTRANET-SRV`, `MAIL-SRV`, DB portals, and `BACKUP-DB-SRV`.
- PC client configuration instructions (DHCP verification via `ipconfig /all`).

### SECTION 5: SECURITY POLICIES & ACL IMPLEMENTATION
- Switchport Port-Security: Commands applied to all access switches (`max 2`, `violation restrict`, `mac-address sticky`).
- Administrative Hardening: RSA keys (`crypto key generate rsa`), local admin user, `transport input ssh`, password encryption, banner MOTD.
- Backup Protection ACL (`BACKUP_PROTECT`):
  * Only permits `ADMIN-PC` (`192.168.50.10`) and production servers (`192.168.40.0/27`) to access Backup VLAN 60 (`192.168.60.0/28`).
  * Explicitly denies all other departments from reaching VLAN 60.
  * Permits all normal internal and outbound traffic.

### SECTION 6: SYSTEMATIC VERIFICATION & HSRP FAILOVER DRILL
- Functional Test Matrix table (Test ID, Source, Target, Action, Expected Result, Pass/Fail Criteria).
- High Availability Failover Simulation: Exact steps to prove HSRP takeover when `CORE-SW1` fails, with packet loss measurements and recovery verification.
- Evidence screenshot list for the final coursework report.

### SECTION 7: VULNERABILITY ASSESSMENT & KALI LINUX TOOLING (30% COMPONENT)
- Identification of 5 specific security vulnerabilities in the campus design.
- Practical demonstration of security tools (Nmap, Wireshark, Yersinia, Hydra, OpenVAS) in an isolated Linux VM testing lab.
- Layer 2 and Layer 3 defensive mitigations (DHCP Snooping, Dynamic ARP Inspection, BPDU Guard, ACLs).
- Enterprise ongoing vulnerability maintenance and patching policy.

Generate the complete guide now with full technical depth and precision.

--- END PROMPT ---
```

