# MSS Ltd Cisco Packet Tracer - Complete Beginner Implementation Guide

## 1. What You Are Building

This guide takes you from an empty Cisco Packet Tracer file to a tested network for MSS Ltd.

The company has:

| Floor | Department | Required PCs |
|---|---|---:|
| First floor | Marketing | 20 |
| Second floor | Sales | 25 |
| Third floor | General Support | 15 |

The finished network will provide:

- A separate VLAN and IP subnet for each department.
- Automatic IP addressing using DHCP.
- Company DNS, intranet and e-mail services.
- Three departmental database servers available to all employees.
- A protected backup database and archive server.
- Internet access through an edge router using NAT overload.
- Two multilayer core switches, dual access-switch uplinks, HSRP and STP redundancy.
- SSH management, port security, ACLs, disabled unused ports and a separate management VLAN.
- A clear testing and screenshot plan for the coursework report.

Packet Tracer does not emulate a real SQL database, enterprise firewall, full backup software, Nessus or Wireshark. Database servers are represented by Packet Tracer servers with HTTP pages, and the report should clearly state this simulation limitation.

## 2. Important Beginner Rules

1. Save after every major stage using **File > Save**.
2. Use the exact device names and addresses in this guide.
3. Configure and test one section before moving to the next.
4. A red link immediately after connection is normal; wait for STP and interfaces to initialise.
5. Commands are entered in a device's **CLI** tab. Press Enter, type `enable`, and then type `configure terminal`.
6. If a command is rejected, check the device model and interface name with `show ip interface brief` or `show interfaces status`.
7. The command `write memory` saves the running configuration.
8. Use passwords supplied by your group, not the example passwords, in the final submission.

## 3. Final Architecture

```text
                              INTERNET-SRV
                            198.51.100.10/24
                                   |
                                ISP-R1
                    G0/0 203.0.113.1/30
                                   |
                    G0/2 203.0.113.2/30
                                EDGE-R1
                   G0/0 /                 \ G0/1
          10.255.0.2/30                   10.255.0.6/30
                 /                           \
       10.255.0.1/30                    10.255.0.5/30
            CORE-SW1 ===== trunk ===== CORE-SW2
                 \        HSRP/STP        /
                  \                     /
       dual trunks to every access/server switch
          |             |             |             |
       Marketing     Sales A/B      Support       Server farm
       VLAN 10       VLAN 20        VLAN 30       VLAN 40/60
       20 PCs        25 PCs         15 PCs        services
```

This is a hierarchical star design. Access switches connect users on each floor, multilayer core switches provide inter-VLAN routing and redundant default gateways, and the edge router provides external routing and NAT.

## 4. Devices To Place

### 4.1 Network devices

| Quantity | Packet Tracer category | Suggested model | Device names |
|---:|---|---|---|
| 2 | Network Devices > Routers | 2911 | `EDGE-R1`, `ISP-R1` |
| 2 | Network Devices > Switches | 3560-24PS multilayer | `CORE-SW1`, `CORE-SW2` |
| 5 | Network Devices > Switches | 2960-24TT | `SW-MKT`, `SW-SALES-A`, `SW-SALES-B`, `SW-SUPPORT`, `SW-SERVER` |

Two Sales switches are used because Sales requires 25 PCs. A 2960-24TT has only 24 FastEthernet user ports; using a second switch also provides spare capacity for growth.

### 4.2 End devices

| Quantity | Device | Names |
|---:|---|---|
| 20 | PC | `MKT-PC01` to `MKT-PC20` |
| 25 | PC | `SALES-PC01` to `SALES-PC25` |
| 15 | PC | `SUP-PC01` to `SUP-PC15` |
| 1 | PC | `ADMIN-PC` |
| 9 | Server | Listed below |

### 4.3 Servers

| Server | Function | IP address | VLAN |
|---|---|---|---:|
| `INFRA-SRV` | DHCP and DNS | `192.168.40.10/27` | 40 |
| `MAIL-SRV` | E-mail | `192.168.40.11/27` | 40 |
| `INTRANET-SRV` | Internal web service | `192.168.40.12/27` | 40 |
| `MKT-DB-SRV` | Simulated Marketing database portal | `192.168.40.20/27` | 40 |
| `SALES-DB-SRV` | Simulated Sales database portal | `192.168.40.21/27` | 40 |
| `SUPPORT-DB-SRV` | Simulated Support database portal | `192.168.40.22/27` | 40 |
| `BACKUP-DB-SRV` | Simulated backup database | `192.168.60.10/28` | 60 |
| `ARCHIVE-SRV` | Backup/archive server | `192.168.60.11/28` | 60 |
| `INTERNET-SRV` | Public Internet test website | `198.51.100.10/24` | External |

## 5. VLAN And Addressing Plan

| VLAN | Name | Subnet | Mask | HSRP gateway | CORE-SW1 | CORE-SW2 |
|---:|---|---|---|---|---|---|
| 10 | MARKETING | `192.168.10.0/27` | `255.255.255.224` | `.1` | `.2` | `.3` |
| 20 | SALES | `192.168.20.0/26` | `255.255.255.192` | `.1` | `.2` | `.3` |
| 30 | SUPPORT | `192.168.30.0/27` | `255.255.255.224` | `.1` | `.2` | `.3` |
| 40 | SERVERS | `192.168.40.0/27` | `255.255.255.224` | `.1` | `.2` | `.3` |
| 50 | MANAGEMENT | `192.168.50.0/28` | `255.255.255.240` | `.1` | `.2` | `.3` |
| 60 | BACKUP | `192.168.60.0/28` | `255.255.255.240` | `.1` | `.2` | `.3` |
| 99 | NATIVE-BLACKHOLE | No user hosts | - | None | None | None |

Routed links:

| Link | Side A | Side B |
|---|---|---|
| CORE-SW1 to EDGE-R1 | `10.255.0.1/30` | `10.255.0.2/30` |
| CORE-SW2 to EDGE-R1 | `10.255.0.5/30` | `10.255.0.6/30` |
| EDGE-R1 to ISP-R1 | `203.0.113.2/30` | `203.0.113.1/30` |
| ISP-R1 to public LAN | `198.51.100.1/24` | `INTERNET-SRV 198.51.100.10/24` |

The ranges `192.0.2.0/24`, `198.51.100.0/24` and `203.0.113.0/24` are documentation ranges, so they are suitable for a lab design.

## 6. Exact Cable And Port Map

Select **Connections** and use **Copper Straight-Through**. Packet Tracer's automatic connection tool may also be used.

### 6.1 Access switch uplinks

| Access switch port | Connect to | Core port |
|---|---|---|
| `SW-MKT Gi0/1` | `CORE-SW1` | `Fa0/1` |
| `SW-MKT Gi0/2` | `CORE-SW2` | `Fa0/1` |
| `SW-SALES-A Gi0/1` | `CORE-SW1` | `Fa0/2` |
| `SW-SALES-A Gi0/2` | `CORE-SW2` | `Fa0/2` |
| `SW-SALES-B Gi0/1` | `CORE-SW1` | `Fa0/3` |
| `SW-SALES-B Gi0/2` | `CORE-SW2` | `Fa0/3` |
| `SW-SUPPORT Gi0/1` | `CORE-SW1` | `Fa0/4` |
| `SW-SUPPORT Gi0/2` | `CORE-SW2` | `Fa0/4` |
| `SW-SERVER Gi0/1` | `CORE-SW1` | `Fa0/5` |
| `SW-SERVER Gi0/2` | `CORE-SW2` | `Fa0/5` |

### 6.2 Core and router links

| Device/port | Connect to | Device/port |
|---|---|---|
| `CORE-SW1 Gi0/1` | core trunk | `CORE-SW2 Gi0/1` |
| `CORE-SW1 Fa0/24` | routed link | `EDGE-R1 Gi0/0` |
| `CORE-SW2 Fa0/24` | routed link | `EDGE-R1 Gi0/1` |
| `EDGE-R1 Gi0/2` | WAN link | `ISP-R1 Gi0/0` |
| `ISP-R1 Gi0/1` | public LAN | `INTERNET-SRV Fa0` |

If your router displays `G0/0/0` instead of `G0/0`, substitute the displayed interface name throughout.

### 6.3 User and server ports

| Switch | Ports | Connected devices | VLAN |
|---|---|---|---:|
| `SW-MKT` | `Fa0/1-20` | 20 Marketing PCs | 10 |
| `SW-SALES-A` | `Fa0/1-24` | Sales PCs 01-24 | 20 |
| `SW-SALES-B` | `Fa0/1` | Sales PC 25 | 20 |
| `SW-SUPPORT` | `Fa0/1-15` | 15 Support PCs | 30 |
| `SW-SERVER` | `Fa0/1-6` | Normal servers | 40 |
| `SW-SERVER` | `Fa0/7-8` | Backup servers | 60 |
| `SW-SERVER` | `Fa0/9` | Admin PC | 50 |

## 7. Stage 1 - Create And Label The Topology

1. Open Cisco Packet Tracer.
2. Choose **File > New**.
3. Save immediately as `MSS_Ltd_Final.pkt`.
4. Place the routers, core switches, access switches, PCs and servers listed above.
5. Click each device name and rename it exactly as shown.
6. Arrange the topology from left to right or top to bottom: Internet, ISP, edge, core, access, users/servers.
7. Use the **Place Note** tool to label the three floors, server room, VLANs and WAN.
8. Connect every cable using the port map in Section 6.
9. Wait until links become green or amber. An amber redundant trunk may be STP blocking and is expected.
10. Save the file.

Checkpoint: every required device is present and every cable follows the port map.

## 8. Stage 2 - Configure The Core Switches

### 8.1 CORE-SW1 base, VLANs and security

Open `CORE-SW1 > CLI` and enter:

```cisco
enable
configure terminal
hostname CORE-SW1
no ip domain-lookup
enable secret ReplaceEnableSecret
service password-encryption
banner motd #Authorised MSS Ltd administrators only#
ip domain-name mss.local
username netadmin privilege 15 secret ReplaceAdminSecret
crypto key generate rsa modulus 1024
ip ssh version 2

line console 0
 logging synchronous
 exec-timeout 10 0
 password ReplaceConsoleSecret
 login
exit

line vty 0 15
 login local
 transport input ssh
 exec-timeout 10 0
exit

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
exit

ip routing
spanning-tree mode rapid-pvst
spanning-tree vlan 10,30,50 root primary
spanning-tree vlan 20,40,60 root secondary
```

### 8.2 CORE-SW1 SVIs and HSRP

```cisco
interface vlan 10
 description MARKETING-GATEWAY
 ip address 192.168.10.2 255.255.255.224
 standby 10 ip 192.168.10.1
 standby 10 priority 110
 standby 10 preempt
 ip helper-address 192.168.40.10
 no shutdown
exit

interface vlan 20
 description SALES-GATEWAY
 ip address 192.168.20.2 255.255.255.192
 standby 20 ip 192.168.20.1
 standby 20 priority 100
 standby 20 preempt
 ip helper-address 192.168.40.10
 no shutdown
exit

interface vlan 30
 description SUPPORT-GATEWAY
 ip address 192.168.30.2 255.255.255.224
 standby 30 ip 192.168.30.1
 standby 30 priority 110
 standby 30 preempt
 ip helper-address 192.168.40.10
 no shutdown
exit

interface vlan 40
 description SERVER-GATEWAY
 ip address 192.168.40.2 255.255.255.224
 standby 40 ip 192.168.40.1
 standby 40 priority 100
 standby 40 preempt
 no shutdown
exit

interface vlan 50
 description MANAGEMENT-GATEWAY
 ip address 192.168.50.2 255.255.255.240
 standby 50 ip 192.168.50.1
 standby 50 priority 110
 standby 50 preempt
 ip helper-address 192.168.40.10
 no shutdown
exit

interface vlan 60
 description BACKUP-GATEWAY
 ip address 192.168.60.2 255.255.255.240
 standby 60 ip 192.168.60.1
 standby 60 priority 100
 standby 60 preempt
 no shutdown
exit
```

### 8.3 CORE-SW1 trunks and edge link

```cisco
interface range fastEthernet 0/1-5
 description ACCESS-SWITCH-TRUNK
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 10,20,30,40,50,60,99
 no shutdown
exit

interface gigabitEthernet 0/1
 description CORE-SW2-TRUNK
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 10,20,30,40,50,60,99
 no shutdown
exit

interface fastEthernet 0/24
 description ROUTED-LINK-TO-EDGE-R1-G0/0
 no switchport
 ip address 10.255.0.1 255.255.255.252
 no shutdown
exit

ip route 0.0.0.0 0.0.0.0 10.255.0.2
end
write memory
```

### 8.4 CORE-SW2 complete configuration

Open `CORE-SW2 > CLI`:

```cisco
enable
configure terminal
hostname CORE-SW2
no ip domain-lookup
enable secret ReplaceEnableSecret
service password-encryption
banner motd #Authorised MSS Ltd administrators only#
ip domain-name mss.local
username netadmin privilege 15 secret ReplaceAdminSecret
crypto key generate rsa modulus 1024
ip ssh version 2

line console 0
 logging synchronous
 exec-timeout 10 0
 password ReplaceConsoleSecret
 login
exit
line vty 0 15
 login local
 transport input ssh
 exec-timeout 10 0
exit

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
exit

ip routing
spanning-tree mode rapid-pvst
spanning-tree vlan 20,40,60 root primary
spanning-tree vlan 10,30,50 root secondary

interface vlan 10
 ip address 192.168.10.3 255.255.255.224
 standby 10 ip 192.168.10.1
 standby 10 priority 100
 standby 10 preempt
 ip helper-address 192.168.40.10
 no shutdown
exit
interface vlan 20
 ip address 192.168.20.3 255.255.255.192
 standby 20 ip 192.168.20.1
 standby 20 priority 110
 standby 20 preempt
 ip helper-address 192.168.40.10
 no shutdown
exit
interface vlan 30
 ip address 192.168.30.3 255.255.255.224
 standby 30 ip 192.168.30.1
 standby 30 priority 100
 standby 30 preempt
 ip helper-address 192.168.40.10
 no shutdown
exit
interface vlan 40
 ip address 192.168.40.3 255.255.255.224
 standby 40 ip 192.168.40.1
 standby 40 priority 110
 standby 40 preempt
 no shutdown
exit
interface vlan 50
 ip address 192.168.50.3 255.255.255.240
 standby 50 ip 192.168.50.1
 standby 50 priority 100
 standby 50 preempt
 ip helper-address 192.168.40.10
 no shutdown
exit
interface vlan 60
 ip address 192.168.60.3 255.255.255.240
 standby 60 ip 192.168.60.1
 standby 60 priority 110
 standby 60 preempt
 no shutdown
exit

interface range fastEthernet 0/1-5
 description ACCESS-SWITCH-TRUNK
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 10,20,30,40,50,60,99
 no shutdown
exit
interface gigabitEthernet 0/1
 description CORE-SW1-TRUNK
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 10,20,30,40,50,60,99
 no shutdown
exit
interface fastEthernet 0/24
 description ROUTED-LINK-TO-EDGE-R1-G0/1
 no switchport
 ip address 10.255.0.5 255.255.255.252
 no shutdown
exit

ip route 0.0.0.0 0.0.0.0 10.255.0.6
end
write memory
```

### 8.5 Check the core

Run on both core switches:

```cisco
show vlan brief
show interfaces trunk
show ip interface brief
show standby brief
show spanning-tree
show ip route
```

Checkpoint: all six HSRP groups show one active and one standby switch. VLANs 10, 30 and 50 should prefer CORE-SW1; VLANs 20, 40 and 60 should prefer CORE-SW2.

## 9. Stage 3 - Configure Access Switches

### 9.1 Common secure base configuration

Repeat this on every access switch, changing the hostname:

```cisco
enable
configure terminal
hostname SW-MKT
no ip domain-lookup
enable secret ReplaceEnableSecret
service password-encryption
ip domain-name mss.local
username netadmin privilege 15 secret ReplaceAdminSecret
crypto key generate rsa modulus 1024
ip ssh version 2
spanning-tree mode rapid-pvst

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
exit

line console 0
 logging synchronous
 password ReplaceConsoleSecret
 login
exit
line vty 0 15
 login local
 transport input ssh
exit

interface gigabitEthernet 0/1
 description TRUNK-TO-CORE-SW1
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 10,20,30,40,50,60,99
 no shutdown
exit
interface gigabitEthernet 0/2
 description TRUNK-TO-CORE-SW2
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 10,20,30,40,50,60,99
 no shutdown
exit
```

### 9.2 Marketing switch

Continue on `SW-MKT`:

```cisco
interface range fastEthernet 0/1-20
 description MARKETING-USER
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast
 spanning-tree bpduguard enable
 switchport port-security
 switchport port-security maximum 1
 switchport port-security mac-address sticky
 switchport port-security violation restrict
exit
interface range fastEthernet 0/21-24
 description UNUSED
 switchport mode access
 switchport access vlan 99
 shutdown
exit
interface vlan 50
 ip address 192.168.50.4 255.255.255.240
 no shutdown
exit
ip default-gateway 192.168.50.1
end
write memory
```

### 9.3 Sales switch A

Use the common configuration with hostname `SW-SALES-A`, then:

```cisco
interface range fastEthernet 0/1-24
 description SALES-USER
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast
 spanning-tree bpduguard enable
 switchport port-security
 switchport port-security maximum 1
 switchport port-security mac-address sticky
 switchport port-security violation restrict
exit
interface vlan 50
 ip address 192.168.50.5 255.255.255.240
 no shutdown
exit
ip default-gateway 192.168.50.1
end
write memory
```

### 9.4 Sales switch B

Use hostname `SW-SALES-B`, then:

```cisco
interface fastEthernet 0/1
 description SALES-USER-25
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast
 spanning-tree bpduguard enable
 switchport port-security
 switchport port-security maximum 1
 switchport port-security mac-address sticky
 switchport port-security violation restrict
exit
interface range fastEthernet 0/2-24
 description UNUSED
 switchport mode access
 switchport access vlan 99
 shutdown
exit
interface vlan 50
 ip address 192.168.50.6 255.255.255.240
 no shutdown
exit
ip default-gateway 192.168.50.1
end
write memory
```

### 9.5 Support switch

Use hostname `SW-SUPPORT`, then:

```cisco
interface range fastEthernet 0/1-15
 description SUPPORT-USER
 switchport mode access
 switchport access vlan 30
 spanning-tree portfast
 spanning-tree bpduguard enable
 switchport port-security
 switchport port-security maximum 1
 switchport port-security mac-address sticky
 switchport port-security violation restrict
exit
interface range fastEthernet 0/16-24
 description UNUSED
 switchport mode access
 switchport access vlan 99
 shutdown
exit
interface vlan 50
 ip address 192.168.50.7 255.255.255.240
 no shutdown
exit
ip default-gateway 192.168.50.1
end
write memory
```

### 9.6 Server switch

Use hostname `SW-SERVER`, then:

```cisco
interface range fastEthernet 0/1-6
 description PRODUCTION-SERVER
 switchport mode access
 switchport access vlan 40
 spanning-tree portfast
 spanning-tree bpduguard enable
exit
interface range fastEthernet 0/7-8
 description BACKUP-SERVER
 switchport mode access
 switchport access vlan 60
 spanning-tree portfast
 spanning-tree bpduguard enable
exit
interface fastEthernet 0/9
 description ADMIN-PC
 switchport mode access
 switchport access vlan 50
 spanning-tree portfast
 spanning-tree bpduguard enable
exit
interface range fastEthernet 0/10-24
 description UNUSED
 switchport mode access
 switchport access vlan 99
 shutdown
exit
interface vlan 50
 ip address 192.168.50.8 255.255.255.240
 no shutdown
exit
ip default-gateway 192.168.50.1
end
write memory
```

Check each access switch:

```cisco
show vlan brief
show interfaces trunk
show port-security
show spanning-tree
```

## 10. Stage 4 - Configure EDGE-R1, ISP-R1 And Internet

### 10.1 EDGE-R1

```cisco
enable
configure terminal
hostname EDGE-R1
no ip domain-lookup
enable secret ReplaceEnableSecret
service password-encryption
ip domain-name mss.local
username netadmin privilege 15 secret ReplaceAdminSecret
crypto key generate rsa modulus 1024
ip ssh version 2

line console 0
 logging synchronous
 password ReplaceConsoleSecret
 login
exit
line vty 0 4
 login local
 transport input ssh
exit

interface gigabitEthernet 0/0
 description TO-CORE-SW1
 ip address 10.255.0.2 255.255.255.252
 ip nat inside
 no shutdown
exit
interface gigabitEthernet 0/1
 description TO-CORE-SW2
 ip address 10.255.0.6 255.255.255.252
 ip nat inside
 no shutdown
exit
interface gigabitEthernet 0/2
 description TO-ISP-R1
 ip address 203.0.113.2 255.255.255.252
 ip nat outside
 no shutdown
exit

ip route 192.168.10.0 255.255.255.224 10.255.0.1
ip route 192.168.10.0 255.255.255.224 10.255.0.5 5
ip route 192.168.20.0 255.255.255.192 10.255.0.5
ip route 192.168.20.0 255.255.255.192 10.255.0.1 5
ip route 192.168.30.0 255.255.255.224 10.255.0.1
ip route 192.168.30.0 255.255.255.224 10.255.0.5 5
ip route 192.168.40.0 255.255.255.224 10.255.0.5
ip route 192.168.40.0 255.255.255.224 10.255.0.1 5
ip route 192.168.50.0 255.255.255.240 10.255.0.1
ip route 192.168.50.0 255.255.255.240 10.255.0.5 5
ip route 192.168.60.0 255.255.255.240 10.255.0.5
ip route 192.168.60.0 255.255.255.240 10.255.0.1 5
ip route 0.0.0.0 0.0.0.0 203.0.113.1

access-list 1 permit 192.168.0.0 0.0.255.255
ip nat inside source list 1 interface gigabitEthernet 0/2 overload
end
write memory
```

The routes with administrative distance `5` are floating backup routes. The preferred path is aligned with the HSRP active core for each VLAN.

### 10.2 ISP-R1

```cisco
enable
configure terminal
hostname ISP-R1
interface gigabitEthernet 0/0
 description TO-EDGE-R1
 ip address 203.0.113.1 255.255.255.252
 no shutdown
exit
interface gigabitEthernet 0/1
 description PUBLIC-SERVER-LAN
 ip address 198.51.100.1 255.255.255.0
 no shutdown
exit
end
write memory
```

The ISP router does not need a route to the private VLANs because replies return to the translated public address on EDGE-R1.

### 10.3 Public Internet server

1. Open `INTERNET-SRV > Desktop > IP Configuration`.
2. Select **Static**.
3. IP address: `198.51.100.10`.
4. Subnet mask: `255.255.255.0`.
5. Default gateway: `198.51.100.1`.
6. DNS server: leave blank.
7. Open **Services > HTTP** and turn HTTP **On**.
8. Edit the page to show `External Internet Test Server` and save it.

Check EDGE-R1:

```cisco
show ip interface brief
show ip route
show ip nat translations
show ip nat statistics
```

## 11. Stage 5 - Configure Servers

For each internal server, open **Desktop > IP Configuration**, choose **Static**, and enter the values below.

| Server | IP | Mask | Gateway | DNS |
|---|---|---|---|---|
| INFRA-SRV | `192.168.40.10` | `255.255.255.224` | `192.168.40.1` | `192.168.40.10` |
| MAIL-SRV | `192.168.40.11` | `255.255.255.224` | `192.168.40.1` | `192.168.40.10` |
| INTRANET-SRV | `192.168.40.12` | `255.255.255.224` | `192.168.40.1` | `192.168.40.10` |
| MKT-DB-SRV | `192.168.40.20` | `255.255.255.224` | `192.168.40.1` | `192.168.40.10` |
| SALES-DB-SRV | `192.168.40.21` | `255.255.255.224` | `192.168.40.1` | `192.168.40.10` |
| SUPPORT-DB-SRV | `192.168.40.22` | `255.255.255.224` | `192.168.40.1` | `192.168.40.10` |
| BACKUP-DB-SRV | `192.168.60.10` | `255.255.255.240` | `192.168.60.1` | `192.168.40.10` |
| ARCHIVE-SRV | `192.168.60.11` | `255.255.255.240` | `192.168.60.1` | `192.168.40.10` |

### 11.1 Configure DHCP on INFRA-SRV

1. Open `INFRA-SRV > Services > DHCP`.
2. Turn the DHCP service **On**.
3. Complete one pool at a time and click **Add** after each pool.

| Pool name | Default gateway | DNS | Start IP | Mask | Maximum users |
|---|---|---|---|---|---:|
| MARKETING | `192.168.10.1` | `192.168.40.10` | `192.168.10.10` | `255.255.255.224` | 20 |
| SALES | `192.168.20.1` | `192.168.40.10` | `192.168.20.10` | `255.255.255.192` | 40 |
| SUPPORT | `192.168.30.1` | `192.168.40.10` | `192.168.30.10` | `255.255.255.224` | 20 |
| MANAGEMENT | `192.168.50.1` | `192.168.40.10` | `192.168.50.10` | `255.255.255.240` | 4 |

The DHCP start addresses avoid gateways, switch management addresses and reserved infrastructure addresses.

### 11.2 Configure DNS on INFRA-SRV

1. Open `INFRA-SRV > Services > DNS`.
2. Turn DNS **On**.
3. Enter each name and address, clicking **Add** after each.

| DNS name | Address |
|---|---|
| `intranet.mss.local` | `192.168.40.12` |
| `mail.mss.local` | `192.168.40.11` |
| `marketing-db.mss.local` | `192.168.40.20` |
| `sales-db.mss.local` | `192.168.40.21` |
| `support-db.mss.local` | `192.168.40.22` |
| `backup-db.mss.local` | `192.168.60.10` |
| `archive.mss.local` | `192.168.60.11` |
| `www.test.net` | `198.51.100.10` |

### 11.3 Configure the intranet

1. Open `INTRANET-SRV > Services > HTTP`.
2. Turn HTTP and HTTPS **On** if both are available.
3. Edit `index.html` with a title such as `MSS Ltd Employee Intranet`.
4. Add short links or text for Marketing, Sales, Support and IT contacts.
5. Click **Save**.

### 11.4 Configure e-mail

1. Open `MAIL-SRV > Services > EMAIL`.
2. Turn SMTP and POP3 **On**.
3. Set domain name to `mss.local` and click **Set**.
4. Add at least these accounts with suitable demonstration passwords:

| Username | Example address |
|---|---|
| `marketing1` | `marketing1@mss.local` |
| `sales1` | `sales1@mss.local` |
| `support1` | `support1@mss.local` |
| `admin` | `admin@mss.local` |

5. On `MKT-PC01`, open **Desktop > Email** and configure the Marketing account. Use `mail.mss.local` for incoming and outgoing servers.
6. Configure Sales and Support accounts on one PC in each department.
7. Send a message from Marketing to Sales, click **Receive** on Sales, and reply.

### 11.5 Simulate the database and backup services

For each DB server:

1. Open **Services > HTTP** and turn HTTP **On**.
2. Change the home page to identify the department, for example `MSS Ltd Marketing Database Portal - authorised staff only`.
3. Save the page.

For `ARCHIVE-SRV`, turn **FTP On** and create one authorised backup account. Use it only to demonstrate that the server is present; do not claim Packet Tracer performs real scheduled backup jobs.

## 12. Stage 6 - Configure All PCs

1. Open each user PC.
2. Select **Desktop > IP Configuration**.
3. Click **DHCP**.
4. Wait for an IP address, mask, gateway and DNS address.
5. Check that Marketing receives `192.168.10.x`, Sales receives `192.168.20.x`, and Support receives `192.168.30.x`.
6. Configure `ADMIN-PC` using DHCP. It should receive `192.168.50.10-13`.

You can configure one sample PC per department first. When that works, repeat for the remaining PCs.

If a PC receives `169.254.x.x`, DHCP failed. Check the access VLAN, trunk allowed VLAN list, SVI status, `ip helper-address`, server gateway and DHCP pool.

## 13. Stage 7 - Apply Security ACLs

Apply ACLs only after basic connectivity works. This makes troubleshooting much easier.

### 13.1 Restrict SSH management to VLAN 50

On every router and switch:

```cisco
enable
configure terminal
ip access-list standard MGMT-SSH-ONLY
 permit 192.168.50.0 0.0.0.15
 deny any
exit
line vty 0 15
 access-class MGMT-SSH-ONLY in
 login local
 transport input ssh
exit
end
write memory
```

On routers with only VTY lines 0-4, use `line vty 0 4`.

### 13.2 Protect the backup VLAN

Normal user VLANs should not directly access the backup systems. Management and production servers may access them.

Configure this ACL on both core switches:

```cisco
enable
configure terminal
ip access-list extended BACKUP-PROTECTION
 permit ip 192.168.50.0 0.0.0.15 192.168.60.0 0.0.0.15
 permit ip 192.168.40.0 0.0.0.31 192.168.60.0 0.0.0.15
 deny ip 192.168.10.0 0.0.0.31 192.168.60.0 0.0.0.15
 deny ip 192.168.20.0 0.0.0.63 192.168.60.0 0.0.0.15
 deny ip 192.168.30.0 0.0.0.31 192.168.60.0 0.0.0.15
 permit ip any any
exit
interface vlan 60
 ip access-group BACKUP-PROTECTION out
exit
end
write memory
```

Do not block user access to VLAN 40 because the brief requires all employees to access all three departmental databases. In a real deployment, database authentication and role-based permissions would control records inside those databases.

Verify:

```cisco
show access-lists
show ip interface vlan 60
```

## 14. Stage 8 - Complete Functional Testing

Use **Desktop > Command Prompt** on PCs. Run each test and record the result.

### 14.1 Basic addressing

On one PC in each department:

```text
ipconfig /all
ping 192.168.10.1
```

Use the correct `.1` gateway for the department. Expected result: DHCP values are correct and gateway ping succeeds.

### 14.2 Interdepartment routing

From `MKT-PC01`, ping the IP of `SALES-PC01` and `SUP-PC01`.

Expected result: success. This proves inter-VLAN routing works.

### 14.3 DNS and intranet

```text
ping intranet.mss.local
```

Then open **Desktop > Web Browser** and browse to:

```text
http://intranet.mss.local
```

Expected result: the MSS Ltd intranet page loads.

### 14.4 Database availability

From one PC in every department, browse to:

```text
http://marketing-db.mss.local
http://sales-db.mss.local
http://support-db.mss.local
```

Expected result: all three pages load from every department.

### 14.5 Backup protection

From a normal user PC:

```text
ping 192.168.60.10
```

Expected result: failure.

From `ADMIN-PC`:

```text
ping 192.168.60.10
```

Expected result: success.

### 14.6 Internet and NAT

From a user PC:

```text
ping 198.51.100.10
```

Then browse to `http://www.test.net`.

On `EDGE-R1`:

```cisco
show ip nat translations
show ip nat statistics
```

Expected result: ping/web works and a NAT translation appears.

### 14.7 E-mail

Send Marketing-to-Sales and Sales-to-Support messages, then receive them on the destination PCs.

Expected result: messages are delivered using `mail.mss.local`.

### 14.8 SSH restriction

From `ADMIN-PC > Command Prompt`:

```text
ssh -l netadmin 192.168.50.4
```

Expected result: login prompt appears.

Try the same from a Marketing PC.

Expected result: connection is refused or times out because only VLAN 50 is permitted.

### 14.9 Redundancy test

1. First confirm continuous pings from a department PC to its gateway, intranet and Internet server.
2. On the active core for that VLAN, shut the relevant access trunk or power off the core switch.
3. Wait for HSRP/STP reconvergence. Packet Tracer may lose several pings.
4. Confirm pings resume through the standby core.
5. Restore the switch or link.
6. Run `show standby brief` and `show spanning-tree` before and after the test.

Expected result: a short interruption is acceptable; connectivity must recover automatically.

## 15. Troubleshooting Order

Always troubleshoot from the bottom upward:

1. **Cable:** Is the cable connected to the intended port? Is the interface green?
2. **Port:** Run `show interfaces status`. Is it connected and not administratively down?
3. **VLAN:** Run `show vlan brief`. Is the access port in the correct VLAN?
4. **Trunk:** Run `show interfaces trunk`. Is the VLAN allowed on both ends?
5. **SVI:** Run `show ip interface brief`. Is the VLAN interface `up/up`?
6. **DHCP:** Check `ip helper-address`, server IP/gateway and the pool's mask/start address.
7. **Gateway:** Does the PC use the HSRP `.1` address?
8. **Route:** Run `show ip route` on cores and routers.
9. **ACL:** Run `show access-lists`; increasing match counters show which rule handled traffic.
10. **NAT:** Check inside/outside interfaces, default route and `show ip nat translations`.
11. **DNS:** Ping the server by IP first, then by name. If IP works but name fails, inspect DNS.
12. **STP/HSRP:** Run `show spanning-tree` and `show standby brief` to identify the forwarding and active devices.

Common fixes:

| Symptom | Likely cause | Fix |
|---|---|---|
| PC gets `169.254.x.x` | DHCP request cannot reach server | Check VLAN, trunks, SVI and helper address |
| SVI is down/down | VLAN has no active port/trunk | Ensure VLAN exists and one member link is active |
| One VLAN cannot cross a trunk | VLAN omitted from allowed list | Add it on both ends |
| Internet IP works but website name fails | DNS problem | Add DNS record and check client DNS address |
| NAT table is empty | NAT roles/ACL/default route wrong | Verify inside, outside, ACL 1 and routing |
| Backup server is reachable from user PC | ACL absent/wrong direction | Apply `BACKUP-PROTECTION` outbound on VLAN 60 |
| SSH fails from Admin PC | RSA/domain/user/VTY issue | Check domain, keys, local user and VTY settings |
| Redundant link is amber | STP is preventing a loop | This is normal; do not force both links forwarding |

## 16. Evidence To Capture For The Report

Create a folder named `MSS_Evidence` and number screenshots in this order:

1. Full logical topology with device and VLAN labels.
2. Physical/floor arrangement.
3. `show vlan brief`.
4. `show interfaces trunk`.
5. `show ip interface brief` from both cores.
6. `show standby brief` showing active/standby roles.
7. `show spanning-tree` showing root and blocked/forwarding links.
8. Successful DHCP details from one PC per department.
9. DHCP pool and DNS record screens.
10. Intranet page loaded by name.
11. All three database pages reached from an employee PC.
12. Successful cross-department e-mail.
13. Failed user-to-backup test and successful admin-to-backup test.
14. Successful Internet page and NAT translation table.
15. Successful Admin SSH and blocked user SSH.
16. Redundancy test before failure, during reconvergence and after recovery.
17. `show port-security` and disabled unused ports.
18. Final saved Packet Tracer topology.

Every screenshot should have a caption that states what was tested, the expected outcome, the actual outcome and why it matters to MSS Ltd.

## 17. How To Write The Implementation Report For High Marks

Keep the report within the coursework's 20-page limit and use the marking headings.

### 17.1 Introduction, aim and overview - 15%

- Identify MSS Ltd, its three floors and 60 employees.
- State the need for Internet, intranet, e-mail, shared databases, backup and resilience.
- State that the aim is a secure, scalable and redundant hierarchical LAN demonstrated in Packet Tracer.

### 17.2 Requirements analysis - 15%

- Convert each business need into a technical requirement.
- Include a requirements table with user counts, services, availability and security.
- Identify assumptions, such as a central server room and one simulated ISP connection.
- Explain Packet Tracer limitations honestly.

### 17.3 Proposed LAN design - 15%

- Include the architecture diagram and a floor-by-floor layout.
- Include the VLAN/subnet table and device inventory.
- Explain why Sales uses a /26 and two access switches.
- Explain future capacity and spare ports.

### 17.4 Detailed design and implementation - 40%

- **Topology:** hierarchical star, dual core, dual uplinks, benefits and trade-offs.
- **Media:** Cat6 to endpoints and fibre recommendation between floors in the real building.
- **Interconnectivity:** 802.1Q trunks, VLANs, inter-VLAN routing, HSRP and Rapid PVST+.
- **Outside connection:** ISP, default routes, NAT overload and the role of a firewall in production.
- **Protocols/security:** IPv4, DHCP, DNS, SMTP/POP3, HTTP/HTTPS, SSH, ACLs and port security.
- **Implementation:** configuration evidence, service tests, ACL tests and failure test results.

Do not fill this section with definitions. Explain why each decision fits MSS Ltd and prove that it works.

### 17.5 Future proposition and summary - 15%

- Add a real next-generation firewall and secondary ISP.
- Replace simulated database/web services with authenticated applications and real DBMS servers.
- Add wireless access points with corporate and guest SSIDs.
- Add IPv6, central AAA, syslog/SIEM, NTP, endpoint management and automated backups.
- Add switch stacks, fibre uplinks and UPS protection when budget permits.
- Conclude by mapping the tested design back to every business requirement.

## 18. Vulnerability Analysis - Separate 30% Coursework Component

### 18.1 Vulnerabilities - 20%

Discuss realistic weaknesses in this design:

- Password attacks against network devices and e-mail accounts.
- Phishing and malware reaching staff endpoints.
- Unpatched server and intranet software.
- Misconfigured ACLs exposing backup systems.
- Rogue devices connected to live switch ports.
- MAC flooding, ARP spoofing, DHCP spoofing and VLAN hopping.
- Unencrypted HTTP/POP3 traffic in the Packet Tracer demonstration.
- Insider access to shared departmental databases.
- Loss of the only ISP or edge router.
- Theft, fire, power loss or hardware failure in the server room.

Rate each item by likelihood and impact, then give an MSS-specific reason.

### 18.2 Tools and techniques - 40%

| Tool | Safe lab use | Evidence to discuss |
|---|---|---|
| Nmap | Discover live hosts, ports and service versions | Unexpected services or exposed management ports |
| Wireshark | Capture approved test traffic | Plain-text protocols, ARP anomalies and unusual flows |
| Nessus/OpenVAS | Authenticated vulnerability scan | Missing patches and unsafe configurations |
| Nikto | Scan the intranet test server | Web headers and common web weaknesses |
| Kali Linux | Controlled test workstation | Central platform for authorised tests |
| Metasploit | Validate a known weakness in an isolated lab | Demonstrate impact only with permission |

Explain scope, written authorisation, backups, rate limits and the rule that production systems must not be disrupted.

### 18.3 Defences - 30%

Map every important vulnerability to a control:

- SSH, central AAA and multifactor authentication for administration.
- Port security, BPDU Guard, DHCP snooping and Dynamic ARP Inspection where supported.
- Least-privilege ACLs and authenticated database roles.
- Firewall and IDS/IPS between MSS Ltd and the Internet.
- Patch management, endpoint protection and e-mail filtering.
- Encrypted protocols such as HTTPS and secure mail in production.
- Offline/immutable backups and regular restore tests.
- UPS, locked racks, environmental monitoring and a disaster recovery plan.
- Security awareness training and phishing exercises.

### 18.4 Maintenance guideline - 10%

| Frequency | Activity |
|---|---|
| Daily | Monitor availability, security alerts and backup job status |
| Weekly | Review logs, failed logins and configuration changes |
| Monthly | Apply approved patches and run vulnerability scans |
| Monthly | Review accounts, permissions, firewall rules and unused ports |
| Quarterly | Restore a sample backup and test failover procedures |
| Quarterly | Conduct a controlled penetration test and phishing exercise |
| Annually | Review risks, capacity, warranties, disaster recovery and policies |
| After any change | Back up configuration and repeat connectivity/security tests |

## 19. Final Submission Checklist

- [ ] Packet Tracer file opens without errors.
- [ ] Exactly 20 Marketing, 25 Sales and 15 Support PCs are present.
- [ ] All PCs receive correct DHCP information.
- [ ] Every PC can reach the intranet, e-mail and three departmental DB portals.
- [ ] Internet access works through NAT.
- [ ] Users cannot access the backup VLAN; Admin can.
- [ ] SSH management is available only from VLAN 50.
- [ ] Unused ports are in VLAN 99 and shut down.
- [ ] HSRP and STP redundancy is configured and tested.
- [ ] Configurations are saved with `write memory`.
- [ ] The `.pkt` file and report have sensible versioned backups.
- [ ] Screenshots are readable and captioned.
- [ ] The report follows the marking headings and remains within 20 pages.
- [ ] Vulnerabilities, testing tools, defences and maintenance are specific to MSS Ltd.
- [ ] A group activity table states each member's contribution.

## 20. Sensible Build Schedule

| Session | Work |
|---:|---|
| 1 | Place, rename and cable all devices; create the addressing table |
| 2 | Configure VLANs, trunks and access ports |
| 3 | Configure SVIs, HSRP, STP and inter-VLAN routing |
| 4 | Configure DHCP, DNS, intranet, DB pages and e-mail |
| 5 | Configure edge/ISP routing, NAT and public server |
| 6 | Apply SSH, port security and ACLs |
| 7 | Run functional, security and failover tests |
| 8 | Capture clean evidence and write the report around the marking scheme |

Build one stage at a time, save versions such as `MSS_01_Cabled.pkt`, `MSS_02_VLANs.pkt` and `MSS_03_Services.pkt`, and do not add security ACLs until ordinary connectivity has passed.
