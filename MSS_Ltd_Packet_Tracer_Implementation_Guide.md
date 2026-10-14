# MSS Ltd Packet Tracer Implementation Guide

> **Current firewall-integrated build:** Use `implementation-steps.md` or `MSS_Ltd_Complete_Beginner_Implementation_Guide.md` for the exact topology, addressing and commands. They place `FW-ASA1` between `EDGE-R1` and the redundant core switches. This shorter overview is retained for coursework structure and background only.

## 1. Coursework Goal

Design and implement a secure LAN for MSS Ltd in Cisco Packet Tracer. The company has three departments on three floors:

| Floor | Department | Users |
|---|---:|---:|
| First floor | Marketing | 20 |
| Second floor | Sales | 25 |
| Third floor | General Support | 15 |

The design must support:

- Internet access for all staff.
- Internal intranet access.
- E-mail service.
- Departmental database servers.
- Company-wide access to all departmental databases.
- Backup database and backup/archive server.
- Security controls and vulnerability analysis.
- Redundancy for system or link failure.
- Future growth without redesigning the whole network.

The report should avoid generic textbook definitions. Every design choice should be explained in relation to MSS Ltd.

## 2. Recommended Network Architecture

Use a hierarchical LAN design:

- Access layer: one switch per floor for user PCs.
- Core/distribution layer: two multilayer switches for routing, VLAN gateways, and redundancy.
- Server farm: dedicated server switch and server VLANs.
- Edge layer: an ASA stateful firewall between the redundant core and the company edge router, with the edge router connected to the ISP.

This gives a realistic medium-sized company design and helps you justify topology, cabling, security, interconnectivity, outside connection, and future expansion.

## 3. Devices To Create In Cisco Packet Tracer

### 3.1 Network Devices

| Device Name | Packet Tracer Device Type | Quantity | Purpose |
|---|---|---:|---|
| ISP-R1 | Router | 1 | Simulates Internet service provider |
| EDGE-R1 | Router | 1 | Company edge router, NAT, default route |
| FW-ASA1 | ASA 5505 firewall | 1 | Stateful perimeter inspection and outside access policy |
| CORE-SW1 | Multilayer switch | 1 | Main inter-VLAN routing and core switching |
| CORE-SW2 | Multilayer switch | 1 | Redundant core switch |
| SW-F1-MKT | Access switch | 1 | Marketing floor switch |
| SW-F2-SALES | Access switch | 1 | Sales floor switch |
| SW-F3-SUPPORT | Access switch | 1 | General Support floor switch |
| SW-SERVER | Access switch | 1 | Server farm switch |

### 3.2 End Devices

| Device Type | Quantity | Placement |
|---|---:|---|
| Marketing PCs | 20 | Floor 1 |
| Sales PCs | 25 | Floor 2 |
| Support PCs | 15 | Floor 3 |
| Admin PC | 1 | IT/server room or management VLAN |

### 3.3 Servers

| Server Name | Suggested IP | VLAN | Service |
|---|---|---:|---|
| DNS-SRV | 192.168.40.10 | 40 | DNS |
| MAIL-SRV | 192.168.40.11 | 40 | E-mail |
| INTRANET-SRV | 192.168.40.12 | 40 | HTTP/HTTPS intranet |
| MKT-DB-SRV | 192.168.40.20 | 40 | Marketing database |
| SALES-DB-SRV | 192.168.40.21 | 40 | Sales database |
| SUPPORT-DB-SRV | 192.168.40.22 | 40 | General Support database |
| BACKUP-DB-SRV | 192.168.60.10 | 60 | Backup database |
| BACKUP-FILE-SRV | 192.168.60.11 | 60 | Backup/archive server |
| SYSLOG-NTP-SRV | 192.168.50.10 | 50 | Optional logging/time server |

## 4. VLAN And IP Addressing Plan

| VLAN | Name | Purpose | Subnet | Default Gateway |
|---:|---|---|---|---|
| 10 | MARKETING | Floor 1 users | 192.168.10.0/27 | 192.168.10.1 |
| 20 | SALES | Floor 2 users | 192.168.20.0/26 | 192.168.20.1 |
| 30 | SUPPORT | Floor 3 users | 192.168.30.0/27 | 192.168.30.1 |
| 40 | SERVERS | Intranet, e-mail, DNS, database servers | 192.168.40.0/27 | 192.168.40.1 |
| 50 | MANAGEMENT | Switch/router/admin management | 192.168.50.0/28 | 192.168.50.1 |
| 60 | BACKUP | Backup database and archive services | 192.168.60.0/28 | 192.168.60.1 |
| 99 | NATIVE-BLACKHOLE | Unused/native VLAN | 192.168.99.0/30 | Not used by users |

Justification:

- VLANs separate departments and reduce unnecessary broadcast traffic.
- Department separation supports security and easier troubleshooting.
- Sales gets a /26 because it has the largest user count and needs more growth space.
- Servers and backup systems are separated so ACLs can restrict access.
- A management VLAN prevents normal users from directly managing network devices.
- VLAN 99 is used as an unused native VLAN to reduce VLAN hopping risk.

## 5. Physical Cabling Plan

Use this in your report:

- Cat6 copper from PCs to access switches.
- Fibre or high-speed copper uplinks between floor switches and core switches.
- Trunk links between access switches and core switches.
- Trunk links between server switch and core switches.
- The firewall connects both core switches to the edge router; the edge router connects to the ISP.

Justification:

- Cat6 is cost-effective and supports modern LAN speeds for workstations.
- Fibre/high-speed uplinks are suitable between floors because they support higher throughput and better distance performance.
- Trunk links carry multiple VLANs from each floor to the core.

## 6. Packet Tracer Build Steps

### Step 1: Create The Topology

1. Add two routers, `ISP-R1` and `EDGE-R1`, plus one ASA 5505 firewall named `FW-ASA1`.
2. Add two multilayer switches: `CORE-SW1` and `CORE-SW2`.
3. Add four access switches:
   - `SW-F1-MKT`
   - `SW-F2-SALES`
   - `SW-F3-SUPPORT`
   - `SW-SERVER`
4. Add PCs:
   - 20 Marketing PCs on Floor 1.
   - 25 Sales PCs on Floor 2.
   - 15 Support PCs on Floor 3.
   - 1 Admin PC.
5. Add the servers listed in section 3.3.

### Step 2: Connect The Devices

Suggested links:

- Marketing PCs to `SW-F1-MKT`.
- Sales PCs to `SW-F2-SALES`.
- Support PCs to `SW-F3-SUPPORT`.
- Servers to `SW-SERVER`.
- Each floor switch to both `CORE-SW1` and `CORE-SW2`.
- `SW-SERVER` to both core switches.
- `CORE-SW1` to `CORE-SW2`.
- Both core switches to `FW-ASA1`, then `FW-ASA1` to `EDGE-R1`.
- `EDGE-R1` to `ISP-R1`.

For redundancy, connect each access switch to both core switches.

### Step 3: Configure VLANs On All Switches

On each switch:

```text
enable
configure terminal
vlan 10
 name MARKETING
vlan 20
 name SALES
vlan 30
 name SUPPORT
vlan 40
 name SERVERS
vlan 50
 name MANAGEMENT
vlan 60
 name BACKUP
vlan 99
 name NATIVE-BLACKHOLE
end
write memory
```

### Step 4: Assign Access Ports

Example for Marketing floor switch:

```text
enable
configure terminal
interface range fastEthernet0/1-20
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast
 spanning-tree bpduguard enable
 switchport port-security
 switchport port-security maximum 2
 switchport port-security violation restrict
 switchport port-security mac-address sticky
exit
interface range fastEthernet0/21-24
 shutdown
exit
end
write memory
```

Use equivalent access VLANs for other switches:

- Sales ports: VLAN 20.
- Support ports: VLAN 30.
- Normal servers: VLAN 40.
- Backup servers: VLAN 60.
- Admin PC: VLAN 50.

### Step 5: Configure Trunk Links

On uplink ports between access switches and core switches:

```text
enable
configure terminal
interface gigabitEthernet0/1
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 10,20,30,40,50,60,99
exit
end
write memory
```

If the switch model does not support `switchport trunk encapsulation dot1q`, skip that line.

### Step 6: Configure Inter-VLAN Routing On CORE-SW1

On `CORE-SW1`:

```text
enable
configure terminal
ip routing

interface vlan 10
 ip address 192.168.10.1 255.255.255.224
 no shutdown

interface vlan 20
 ip address 192.168.20.1 255.255.255.192
 no shutdown

interface vlan 30
 ip address 192.168.30.1 255.255.255.224
 no shutdown

interface vlan 40
 ip address 192.168.40.1 255.255.255.224
 no shutdown

interface vlan 50
 ip address 192.168.50.1 255.255.255.240
 no shutdown

interface vlan 60
 ip address 192.168.60.1 255.255.255.240
 no shutdown

end
write memory
```

If you also configure `CORE-SW2`, use different SVI IP addresses, for example `.2`, and use HSRP if your Packet Tracer device supports it. If HSRP is too difficult, still show the second core switch and dual uplinks as physical redundancy.

### Step 7: Configure DHCP

Option A: configure DHCP on `CORE-SW1`.

```text
enable
configure terminal

ip dhcp excluded-address 192.168.10.1 192.168.10.10
ip dhcp excluded-address 192.168.20.1 192.168.20.10
ip dhcp excluded-address 192.168.30.1 192.168.30.10
ip dhcp excluded-address 192.168.50.1 192.168.50.5

ip dhcp pool MARKETING
 network 192.168.10.0 255.255.255.224
 default-router 192.168.10.1
 dns-server 192.168.40.10
 domain-name mss.local

ip dhcp pool SALES
 network 192.168.20.0 255.255.255.192
 default-router 192.168.20.1
 dns-server 192.168.40.10
 domain-name mss.local

ip dhcp pool SUPPORT
 network 192.168.30.0 255.255.255.224
 default-router 192.168.30.1
 dns-server 192.168.40.10
 domain-name mss.local

ip dhcp pool MANAGEMENT
 network 192.168.50.0 255.255.255.240
 default-router 192.168.50.1
 dns-server 192.168.40.10
 domain-name mss.local

end
write memory
```

Option B: configure DHCP on a server in VLAN 40. If you do this, add `ip helper-address 192.168.40.10` or the DHCP server address under each VLAN interface.

### Step 8: Configure Server Static IP Addresses

In Packet Tracer, open each server and configure:

- Static IP address.
- Subnet mask.
- Default gateway.
- DNS server address.

Example for `INTRANET-SRV`:

```text
IP address: 192.168.40.12
Subnet mask: 255.255.255.224
Default gateway: 192.168.40.1
DNS server: 192.168.40.10
```

### Step 9: Configure DNS

On `DNS-SRV`, enable DNS and create records:

| Name | Address |
|---|---|
| intranet.mss.local | 192.168.40.12 |
| mail.mss.local | 192.168.40.11 |
| db-marketing.mss.local | 192.168.40.20 |
| db-sales.mss.local | 192.168.40.21 |
| db-support.mss.local | 192.168.40.22 |
| backup.mss.local | 192.168.60.11 |

### Step 10: Configure Intranet And E-mail

On `INTRANET-SRV`:

- Enable HTTP and HTTPS if available.
- Create a simple intranet page stating MSS Ltd intranet.

On `MAIL-SRV`:

- Enable e-mail service.
- Create sample users, for example:
  - `marketing1@mss.local`
  - `sales1@mss.local`
  - `support1@mss.local`

On sample PCs:

- Configure e-mail clients.
- Send test e-mails between departments.

### Step 11: Configure Firewall And Internet Edge Routing

Use the firewall-integrated routed links below:

| Link | Addressing |
|---|---|
| CORE-SW1 Fa0/24 to FW-ASA1 E0/1 | CORE `.2/29`, ASA inside `.1/29` |
| CORE-SW2 Fa0/24 to FW-ASA1 E0/2 | CORE `.3/29`, same ASA inside VLAN; transit HSRP `.4/29` |
| FW-ASA1 E0/0 to EDGE-R1 G0/0 | ASA outside `10.255.1.2/30`, EDGE `10.255.1.1/30` |
| EDGE-R1 G0/2 to ISP-R1 G0/0 | EDGE `203.0.113.2/30`, ISP `203.0.113.1/30` |

Configure `FW-ASA1` with inside security level 100, outside security level 0, a default route to `10.255.1.1`, an internal summary route through HSRP address `10.255.0.4`, an outside deny policy, ICMP inspection and SSH restricted to VLAN 50. Keep NAT overload on `EDGE-R1` and route `192.168.0.0/16` from the edge router to `10.255.1.2`.

Use the exact commands in [MSS_Ltd_Complete_Beginner_Implementation_Guide.md](MSS_Ltd_Complete_Beginner_Implementation_Guide.md#101-fw-asa1-stateful-firewall) or [implementation-steps.md](implementation-steps.md#71-configure-fw-asa1). Do not reuse the earlier direct core-to-edge addressing after installing the firewall.

### Step 12: Configure ISP Router

On `ISP-R1`:

```text
enable
configure terminal
interface gigabitEthernet0/0
 ip address 203.0.113.1 255.255.255.252
 no shutdown
exit

interface loopback0
 ip address 8.8.8.8 255.255.255.255
exit

end
write memory
```

The loopback address can be used as a simple Internet test target.

## 7. Security Configuration

### 7.1 Management Security

On routers and switches:

```text
enable
configure terminal
hostname DEVICE-NAME
enable secret StrongEnablePass123
service password-encryption
ip domain-name mss.local
username admin secret AdminStrongPass123
crypto key generate rsa
1024
line vty 0 4
 login local
 transport input ssh
exit
line console 0
 password ConsolePass123
 login
exit
end
write memory
```

### 7.2 Restrict Device Management To Admin VLAN

Example ACL:

```text
ip access-list standard MGMT_ONLY
 permit 192.168.50.0 0.0.0.15
 deny any

line vty 0 4
 access-class MGMT_ONLY in
```

### 7.3 Protect Backup Servers

The backup servers should not be directly accessible from normal user VLANs.

Example ACL on `CORE-SW1`:

```text
ip access-list extended PROTECT_BACKUP
 permit ip 192.168.50.0 0.0.0.15 192.168.60.0 0.0.0.15
 permit ip 192.168.40.0 0.0.0.31 192.168.60.0 0.0.0.15
 deny ip 192.168.10.0 0.0.0.31 192.168.60.0 0.0.0.15
 deny ip 192.168.20.0 0.0.0.63 192.168.60.0 0.0.0.15
 deny ip 192.168.30.0 0.0.0.31 192.168.60.0 0.0.0.15
 permit ip any any
```

Apply it near the backup VLAN interface:

```text
interface vlan 60
 ip access-group PROTECT_BACKUP out
```

### 7.4 Allow Business Database Access

The coursework states that all employees should access all department databases. Do not block user VLANs from database servers in VLAN 40. Instead, explain that server-level authentication controls database permissions.

### 7.5 Switch Hardening

Use these controls:

- Port security on user ports.
- Shutdown unused ports.
- BPDU Guard and PortFast on access ports.
- Non-user native VLAN 99.
- SSH instead of Telnet.
- Strong passwords.
- Management access restricted to VLAN 50.

## 8. Redundancy Plan

Implement or explain:

- Two core switches.
- Dual uplinks from every access switch to the core.
- STP to prevent loops.
- Optional EtherChannel for bandwidth and link redundancy.
- Optional HSRP for default gateway redundancy.
- Backup database server.
- Backup/archive server.
- Regular scheduled backups.

If HSRP is implemented, use virtual gateways such as:

| VLAN | HSRP Virtual Gateway |
|---:|---|
| 10 | 192.168.10.1 |
| 20 | 192.168.20.1 |
| 30 | 192.168.30.1 |
| 40 | 192.168.40.1 |
| 50 | 192.168.50.1 |
| 60 | 192.168.60.1 |

Then give CORE-SW1 and CORE-SW2 real addresses such as `.2` and `.3`.

## 9. Testing Plan

Take screenshots for each important test.

| Test | Expected Result |
|---|---|
| Marketing PC receives DHCP address | Address from 192.168.10.0/27 |
| Sales PC receives DHCP address | Address from 192.168.20.0/26 |
| Support PC receives DHCP address | Address from 192.168.30.0/27 |
| PC pings own gateway | Success |
| Marketing PC pings Sales PC | Success if interdepartment communication is allowed |
| PC opens intranet.mss.local | Success |
| PC reaches mail.mss.local | Success |
| PC reaches database servers | Success |
| Normal PC reaches backup server | Blocked |
| Admin PC reaches backup server | Success |
| Normal PC SSH to switch/router | Blocked |
| Admin PC SSH to switch/router | Success |
| PC pings ISP loopback 8.8.8.8 | Success through NAT |
| One uplink disconnected | Network remains available if redundancy is configured |

## 10. Vulnerability Analysis Section

Include this as the second major part of the report.

### 10.1 Likely Vulnerabilities

- Weak device passwords.
- Telnet enabled instead of SSH.
- Poor VLAN configuration or native VLAN misuse.
- VLAN hopping.
- ARP spoofing.
- Packet sniffing on insecure segments.
- Unpatched servers.
- Intranet web server vulnerabilities.
- Database exposure.
- Insecure backup server access.
- E-mail phishing and malware.
- Lack of monitoring/logging.
- Single Internet link failure.

### 10.2 Suitable Tools

| Tool | Use In This Scenario |
|---|---|
| Nmap | Discover hosts, open ports, and exposed services |
| Wireshark | Inspect traffic, detect unencrypted protocols and suspicious packets |
| OpenVAS or Nessus | Vulnerability scanning against servers and network devices |
| Nikto | Web/intranet server vulnerability checks |
| Metasploit | Controlled exploitation demonstration in a lab |
| Hydra | Password strength testing against lab services |
| Kali Linux | Main vulnerability testing platform |

### 10.3 Defence Mechanisms

- Use SSH only.
- Use strong passwords and local user accounts.
- Restrict management access to VLAN 50.
- Use ACLs between VLANs.
- Protect backup servers from normal users.
- Apply port security.
- Disable unused ports.
- Use a non-user native VLAN.
- Patch servers regularly.
- Use antivirus/endpoint protection.
- Keep verified backups.
- Monitor logs and network events.
- Train users against phishing.

### 10.4 Regular Maintenance Guideline

| Frequency | Activity |
|---|---|
| Daily | Check critical server and backup status |
| Weekly | Review logs and verify backup completion |
| Monthly | Run vulnerability scans |
| Monthly | Check user accounts and access permissions |
| Quarterly | Conduct controlled penetration test |
| Quarterly | Review firewall and ACL rules |
| After major changes | Re-test connectivity and security controls |

## 11. Report Structure For Maximum Marks

Use the exact marking structure:

1. Introduction, aim, and overview.
2. Analysis of present scenario and company requirements.
3. Proposed LAN design, including building/floor plan and layout.
4. Detailed proposed LAN design:
   - Topology/architecture.
   - Media/cabling.
   - Interconnectivity between floors.
   - Outside connection.
   - Protocol/software/security.
   - Packet Tracer implementation.
5. Future proposition.
6. Summary/conclusion.
7. Vulnerability analysis.
8. Defence mechanisms.
9. Maintenance guideline.
10. Group activity list appendix.

## 12. Screenshots To Include In The Report

Capture these from Packet Tracer:

- Full logical topology.
- Physical/floor layout.
- VLAN table from switches.
- Trunk status.
- IP addressing table.
- DHCP successful address assignment.
- Server configuration screens.
- DNS records.
- Intranet webpage access from a PC.
- E-mail test between departments.
- Successful database reachability.
- Blocked backup access from normal user VLAN.
- Successful admin access from management VLAN.
- NAT/Internet connectivity test.
- Redundancy test, if implemented.

## 13. Final Quality Checklist

Before submission:

- Every department has the correct number of PCs.
- Every floor has its own access switch.
- VLANs are labelled clearly.
- Gateways and IP addressing are consistent.
- Servers use static IP addresses.
- DHCP works for all user VLANs.
- Intranet, e-mail, DNS, and database services work.
- Backup server access is restricted.
- Internet access works through NAT.
- Security settings are visible and testable.
- Screenshots prove the design works.
- Report explanations are specific to MSS Ltd, not generic textbook content.
- Vulnerability analysis includes tools, examples, defences, and maintenance.

