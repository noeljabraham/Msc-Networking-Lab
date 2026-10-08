# MSS Ltd Full Implementation Steps

This file gives a complete start-to-end implementation order for the MSS Ltd Cisco Packet Tracer network. It follows the intended top-to-bottom flow:

1. Internet and ISP
2. Edge router
3. Redundant core switches
4. Access switches
5. Server farm
6. Department PCs
7. Security controls
8. Functional testing
9. Redundancy testing
10. Evidence and final checks

The design supports the coursework requirements for Marketing, Sales, General Support, intranet, e-mail, departmental database access, backup services, Internet access, redundancy, security and future growth.

---

## 1. Final Device List

### 1.1 Routers

| Device | Packet Tracer model | Purpose |
|---|---|---|
| ISP-R1 | 2911 router | Simulates the external ISP network |
| EDGE-R1 | 2911 router | Company edge router, NAT and default Internet route |

### 1.2 Core switches

| Device | Packet Tracer model | Purpose |
|---|---|---|
| CORE-SW1 | 3560-24PS multilayer switch | Main core switch, inter-VLAN routing, HSRP and STP |
| CORE-SW2 | 3560-24PS multilayer switch | Redundant core switch, HSRP and STP backup/load sharing |

### 1.3 Access switches

| Device | Packet Tracer model | Purpose |
|---|---|---|
| SW-MKT | 2960-24TT | Floor 1 Marketing users |
| SW-SALES-A | 2960-24TT | Floor 2 Sales users 01-24 |
| SW-SALES-B | 2960-24TT | Floor 2 Sales user 25 and future growth |
| SW-SUPPORT | 2960-24TT | Floor 3 General Support users |
| SW-SERVER | 2960-24TT | Server farm, backup VLAN and Admin PC |

### 1.4 End devices

| Device group | Quantity | Names |
|---|---:|---|
| Marketing PCs | 20 | MKT-PC01 to MKT-PC20 |
| Sales PCs | 25 | SALES-PC01 to SALES-PC25 |
| Support PCs | 15 | SUP-PC01 to SUP-PC15 |
| Admin PC | 1 | ADMIN-PC |

### 1.5 Servers

| Server | Purpose | IP address | VLAN |
|---|---|---|---:|
| INFRA-SRV | DHCP and DNS | 192.168.40.10/27 | 40 |
| MAIL-SRV | E-mail service | 192.168.40.11/27 | 40 |
| INTRANET-SRV | Internal web/intranet service | 192.168.40.12/27 | 40 |
| MKT-DB-SRV | Simulated Marketing database portal | 192.168.40.20/27 | 40 |
| SALES-DB-SRV | Simulated Sales database portal | 192.168.40.21/27 | 40 |
| SUPPORT-DB-SRV | Simulated Support database portal | 192.168.40.22/27 | 40 |
| BACKUP-DB-SRV | Simulated backup database | 192.168.60.10/28 | 60 |
| ARCHIVE-SRV | Backup/archive server | 192.168.60.11/28 | 60 |
| INTERNET-SRV | External test website | 198.51.100.10/24 | External |

---

## 2. VLAN And Addressing Plan

| VLAN | Name | Subnet | Mask | HSRP gateway | CORE-SW1 | CORE-SW2 |
|---:|---|---|---|---|---|---|
| 10 | MARKETING | 192.168.10.0/27 | 255.255.255.224 | 192.168.10.1 | 192.168.10.2 | 192.168.10.3 |
| 20 | SALES | 192.168.20.0/26 | 255.255.255.192 | 192.168.20.1 | 192.168.20.2 | 192.168.20.3 |
| 30 | SUPPORT | 192.168.30.0/27 | 255.255.255.224 | 192.168.30.1 | 192.168.30.2 | 192.168.30.3 |
| 40 | SERVERS | 192.168.40.0/27 | 255.255.255.224 | 192.168.40.1 | 192.168.40.2 | 192.168.40.3 |
| 50 | MANAGEMENT | 192.168.50.0/28 | 255.255.255.240 | 192.168.50.1 | 192.168.50.2 | 192.168.50.3 |
| 60 | BACKUP | 192.168.60.0/28 | 255.255.255.240 | 192.168.60.1 | 192.168.60.2 | 192.168.60.3 |
| 99 | NATIVE-BLACKHOLE | No user hosts | Not used | None | None | None |

### 2.1 Routed links

| Link | Side A | Side B |
|---|---|---|
| CORE-SW1 to EDGE-R1 | CORE-SW1 Fa0/24: 10.255.0.1/30 | EDGE-R1 G0/0: 10.255.0.2/30 |
| CORE-SW2 to EDGE-R1 | CORE-SW2 Fa0/24: 10.255.0.5/30 | EDGE-R1 G0/1: 10.255.0.6/30 |
| EDGE-R1 to ISP-R1 | EDGE-R1 G0/2: 203.0.113.2/30 | ISP-R1 G0/0: 203.0.113.1/30 |
| ISP-R1 to Internet server | ISP-R1 G0/1: 198.51.100.1/24 | INTERNET-SRV Fa0: 198.51.100.10/24 |

---

## 3. Build The Topology From Top To Bottom

### Step 3.1 - Create the Packet Tracer file

1. Open Cisco Packet Tracer.
2. Create a new file.
3. Save it as `MSS_Ltd_Final.pkt`.
4. Place the devices from the final device list.
5. Rename every device exactly as listed.
6. Arrange the topology from top to bottom:
   - INTERNET-SRV
   - ISP-R1
   - EDGE-R1
   - CORE-SW1 and CORE-SW2
   - Access switches
   - Servers and PCs
7. Add labels for:
   - Floor 1 Marketing
   - Floor 2 Sales
   - Floor 3 General Support
   - Server farm
   - VLAN 10, 20, 30, 40, 50, 60 and 99
   - WAN/Internet link
8. Save the file.

### Step 3.2 - Cable the Internet and edge layer

Use copper straight-through unless Packet Tracer requires another cable type.

| From | To |
|---|---|
| INTERNET-SRV Fa0 | ISP-R1 G0/1 |
| ISP-R1 G0/0 | EDGE-R1 G0/2 |
| EDGE-R1 G0/0 | CORE-SW1 Fa0/24 |
| EDGE-R1 G0/1 | CORE-SW2 Fa0/24 |

### Step 3.3 - Cable the core switches

| From | To |
|---|---|
| CORE-SW1 Gi0/1 | CORE-SW2 Gi0/1 |

This link is the core-to-core trunk used for VLAN reachability and redundancy.

### Step 3.4 - Cable the access switches to both cores

| Access switch port | Connects to | Core port |
|---|---|---|
| SW-MKT Gi0/1 | CORE-SW1 | Fa0/1 |
| SW-MKT Gi0/2 | CORE-SW2 | Fa0/1 |
| SW-SALES-A Gi0/1 | CORE-SW1 | Fa0/2 |
| SW-SALES-A Gi0/2 | CORE-SW2 | Fa0/2 |
| SW-SALES-B Gi0/1 | CORE-SW1 | Fa0/3 |
| SW-SALES-B Gi0/2 | CORE-SW2 | Fa0/3 |
| SW-SUPPORT Gi0/1 | CORE-SW1 | Fa0/4 |
| SW-SUPPORT Gi0/2 | CORE-SW2 | Fa0/4 |
| SW-SERVER Gi0/1 | CORE-SW1 | Fa0/5 |
| SW-SERVER Gi0/2 | CORE-SW2 | Fa0/5 |

### Step 3.5 - Cable users and servers

| Switch | Ports | Connected devices | VLAN |
|---|---|---|---:|
| SW-MKT | Fa0/1-20 | MKT-PC01 to MKT-PC20 | 10 |
| SW-SALES-A | Fa0/1-24 | SALES-PC01 to SALES-PC24 | 20 |
| SW-SALES-B | Fa0/1 | SALES-PC25 | 20 |
| SW-SUPPORT | Fa0/1-15 | SUP-PC01 to SUP-PC15 | 30 |
| SW-SERVER | Fa0/1-6 | INFRA-SRV, MAIL-SRV, INTRANET-SRV, MKT-DB-SRV, SALES-DB-SRV, SUPPORT-DB-SRV | 40 |
| SW-SERVER | Fa0/7-8 | BACKUP-DB-SRV, ARCHIVE-SRV | 60 |
| SW-SERVER | Fa0/9 | ADMIN-PC | 50 |

Save the Packet Tracer file after cabling.

---

## 4. Configure CORE-SW1

Open CORE-SW1 CLI and enter:

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

Configure CORE-SW1 VLAN interfaces and HSRP:

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

Configure CORE-SW1 trunks and routed edge link:

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

---

## 5. Configure CORE-SW2

Open CORE-SW2 CLI and enter:

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

Check both core switches:

```cisco
show vlan brief
show interfaces trunk
show ip interface brief
show standby brief
show spanning-tree
show ip route
```

Expected result:

- VLANs exist.
- Trunks are up.
- SVIs are up/up.
- HSRP shows one active and one standby switch.
- CORE-SW1 is preferred for VLANs 10, 30 and 50.
- CORE-SW2 is preferred for VLANs 20, 40 and 60.

---

## 6. Configure Access Switches

Apply this common base configuration to every access switch. Change the hostname for each switch.

```cisco
enable
configure terminal
hostname SWITCH-NAME
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

### Step 6.1 - Configure SW-MKT

Use the common base configuration with hostname `SW-MKT`, then enter:

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

### Step 6.2 - Configure SW-SALES-A

Use the common base configuration with hostname `SW-SALES-A`, then enter:

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

### Step 6.3 - Configure SW-SALES-B

Use the common base configuration with hostname `SW-SALES-B`, then enter:

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

### Step 6.4 - Configure SW-SUPPORT

Use the common base configuration with hostname `SW-SUPPORT`, then enter:

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

### Step 6.5 - Configure SW-SERVER

Use the common base configuration with hostname `SW-SERVER`, then enter:

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

---

## 7. Configure EDGE-R1

Open EDGE-R1 CLI and enter:

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

Check EDGE-R1:

```cisco
show ip interface brief
show ip route
show ip nat statistics
```

---

## 8. Configure ISP-R1 And External Internet Server

### Step 8.1 - Configure ISP-R1

Open ISP-R1 CLI and enter:

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

### Step 8.2 - Configure INTERNET-SRV

1. Open INTERNET-SRV.
2. Go to Desktop > IP Configuration.
3. Select Static.
4. Enter:
   - IP address: 198.51.100.10
   - Subnet mask: 255.255.255.0
   - Default gateway: 198.51.100.1
5. Go to Services > HTTP.
6. Turn HTTP On.
7. Edit the page to show `External Internet Test Server`.
8. Save the page.

---

## 9. Configure Internal Servers

For each internal server, open Desktop > IP Configuration and select Static.

| Server | IP | Mask | Gateway | DNS |
|---|---|---|---|---|
| INFRA-SRV | 192.168.40.10 | 255.255.255.224 | 192.168.40.1 | 192.168.40.10 |
| MAIL-SRV | 192.168.40.11 | 255.255.255.224 | 192.168.40.1 | 192.168.40.10 |
| INTRANET-SRV | 192.168.40.12 | 255.255.255.224 | 192.168.40.1 | 192.168.40.10 |
| MKT-DB-SRV | 192.168.40.20 | 255.255.255.224 | 192.168.40.1 | 192.168.40.10 |
| SALES-DB-SRV | 192.168.40.21 | 255.255.255.224 | 192.168.40.1 | 192.168.40.10 |
| SUPPORT-DB-SRV | 192.168.40.22 | 255.255.255.224 | 192.168.40.1 | 192.168.40.10 |
| BACKUP-DB-SRV | 192.168.60.10 | 255.255.255.240 | 192.168.60.1 | 192.168.40.10 |
| ARCHIVE-SRV | 192.168.60.11 | 255.255.255.240 | 192.168.60.1 | 192.168.40.10 |

### Step 9.1 - Configure DHCP on INFRA-SRV

1. Open INFRA-SRV.
2. Go to Services > DHCP.
3. Turn DHCP On.
4. Add these pools one by one.

| Pool | Default gateway | DNS | Start IP | Mask | Maximum users |
|---|---|---|---|---|---:|
| MARKETING | 192.168.10.1 | 192.168.40.10 | 192.168.10.10 | 255.255.255.224 | 20 |
| SALES | 192.168.20.1 | 192.168.40.10 | 192.168.20.10 | 255.255.255.192 | 40 |
| SUPPORT | 192.168.30.1 | 192.168.40.10 | 192.168.30.10 | 255.255.255.224 | 20 |
| MANAGEMENT | 192.168.50.1 | 192.168.40.10 | 192.168.50.10 | 255.255.255.240 | 4 |

### Step 9.2 - Configure DNS on INFRA-SRV

1. Open INFRA-SRV.
2. Go to Services > DNS.
3. Turn DNS On.
4. Add these records.

| DNS name | Address |
|---|---|
| intranet.mss.local | 192.168.40.12 |
| mail.mss.local | 192.168.40.11 |
| marketing-db.mss.local | 192.168.40.20 |
| sales-db.mss.local | 192.168.40.21 |
| support-db.mss.local | 192.168.40.22 |
| backup-db.mss.local | 192.168.60.10 |
| archive.mss.local | 192.168.60.11 |
| www.test.net | 198.51.100.10 |

### Step 9.3 - Configure INTRANET-SRV

1. Open INTRANET-SRV.
2. Go to Services > HTTP.
3. Turn HTTP On.
4. Turn HTTPS On if Packet Tracer supports it.
5. Edit index.html.
6. Add a simple page title: `MSS Ltd Employee Intranet`.
7. Add short text for Marketing, Sales, Support and IT contacts.
8. Save the page.

### Step 9.4 - Configure MAIL-SRV

1. Open MAIL-SRV.
2. Go to Services > EMAIL.
3. Turn SMTP On.
4. Turn POP3 On.
5. Set the domain to `mss.local`.
6. Add these accounts:

| Username | Address |
|---|---|
| marketing1 | marketing1@mss.local |
| sales1 | sales1@mss.local |
| support1 | support1@mss.local |
| admin | admin@mss.local |

Use suitable demonstration passwords and record them for the report.

### Step 9.5 - Configure database portals

Packet Tracer does not provide a real SQL database service, so represent each database as an HTTP portal.

For MKT-DB-SRV:

1. Open Services > HTTP.
2. Turn HTTP On.
3. Edit index.html to show `MSS Ltd Marketing Database Portal - authorised staff only`.
4. Save.

For SALES-DB-SRV:

1. Open Services > HTTP.
2. Turn HTTP On.
3. Edit index.html to show `MSS Ltd Sales Database Portal - authorised staff only`.
4. Save.

For SUPPORT-DB-SRV:

1. Open Services > HTTP.
2. Turn HTTP On.
3. Edit index.html to show `MSS Ltd Support Database Portal - authorised staff only`.
4. Save.

### Step 9.6 - Configure backup/archive services

For BACKUP-DB-SRV:

1. Open Services > HTTP.
2. Turn HTTP On.
3. Edit index.html to show `MSS Ltd Backup Database - restricted access`.
4. Save.

For ARCHIVE-SRV:

1. Open Services > FTP.
2. Turn FTP On.
3. Create one authorised backup account.
4. Record the username for demonstration.

Important report note: Packet Tracer only simulates the presence of database and archive services. It does not perform real scheduled backups or real database replication.

---

## 10. Configure All PCs

### Step 10.1 - Configure user PCs with DHCP

For every Marketing, Sales and Support PC:

1. Open the PC.
2. Go to Desktop > IP Configuration.
3. Click DHCP.
4. Wait for IP, mask, gateway and DNS values.
5. Confirm the correct addressing:
   - Marketing PCs receive 192.168.10.x
   - Sales PCs receive 192.168.20.x
   - Support PCs receive 192.168.30.x

### Step 10.2 - Configure ADMIN-PC

1. Open ADMIN-PC.
2. Go to Desktop > IP Configuration.
3. Click DHCP.
4. Confirm it receives an address from 192.168.50.10 to 192.168.50.13.
5. Confirm gateway is 192.168.50.1.
6. Confirm DNS is 192.168.40.10.

### Step 10.3 - Configure e-mail clients

On MKT-PC01:

1. Open Desktop > Email.
2. Configure:
   - Email address: marketing1@mss.local
   - Incoming mail server: mail.mss.local
   - Outgoing mail server: mail.mss.local

On SALES-PC01:

1. Configure sales1@mss.local.
2. Use mail.mss.local for incoming and outgoing server.

On SUP-PC01:

1. Configure support1@mss.local.
2. Use mail.mss.local for incoming and outgoing server.

On ADMIN-PC:

1. Configure admin@mss.local.
2. Use mail.mss.local for incoming and outgoing server.

---

## 11. Apply Security Controls

Apply ACLs only after normal routing, DHCP, DNS, intranet, database and Internet connectivity are working.

### Step 11.1 - Restrict SSH management to VLAN 50

Apply this on every router and switch:

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

On routers that only support VTY 0-4, use:

```cisco
line vty 0 4
```

### Step 11.2 - Protect the backup VLAN

Apply this on both CORE-SW1 and CORE-SW2:

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

This blocks normal users from directly reaching backup systems while allowing the management VLAN and production server VLAN.

Do not block user access to VLAN 40 because the coursework requires all employees to access all department databases. In the report, explain that database permissions would be controlled by application authentication and database roles in a real deployment.

### Step 11.3 - Confirm switch hardening

Confirm these protections exist:

| Control | Where applied | Purpose |
|---|---|---|
| SSH only | Routers and switches | Prevent Telnet management |
| Local admin user | Routers and switches | Authenticated administration |
| VLAN 50 management ACL | VTY lines | Only Admin VLAN can manage devices |
| Port security | User access ports | Limit rogue endpoint connections |
| PortFast | User/server access ports | Faster host connection |
| BPDU Guard | User/server access ports | Stops accidental switch loops |
| Unused ports shutdown | Access switches | Reduces attack surface |
| VLAN 99 native VLAN | Trunks | Reduces VLAN hopping risk |
| Backup ACL | VLAN 60 SVI | Protects backup systems |

---

## 12. Functional Testing From Start To End

Create a folder named `MSS_Evidence` and capture screenshots as you test.

### Step 12.1 - Check VLANs and trunks

On core and access switches:

```cisco
show vlan brief
show interfaces trunk
show spanning-tree
```

Expected result:

- VLANs 10, 20, 30, 40, 50, 60 and 99 exist.
- Trunks allow VLANs 10,20,30,40,50,60,99.
- Redundant links may show an STP blocking state, which is normal.

### Step 12.2 - Check HSRP and routing

On both core switches:

```cisco
show standby brief
show ip route
show ip interface brief
```

Expected result:

- HSRP groups show active/standby roles.
- SVIs are up/up.
- Default route points to EDGE-R1.

### Step 12.3 - Check DHCP

On one PC in each department:

```text
ipconfig /all
```

Expected result:

- MKT-PC01 has 192.168.10.x, gateway 192.168.10.1 and DNS 192.168.40.10.
- SALES-PC01 has 192.168.20.x, gateway 192.168.20.1 and DNS 192.168.40.10.
- SUP-PC01 has 192.168.30.x, gateway 192.168.30.1 and DNS 192.168.40.10.
- ADMIN-PC has 192.168.50.x, gateway 192.168.50.1 and DNS 192.168.40.10.

### Step 12.4 - Check gateway connectivity

From each department PC:

```text
ping 192.168.10.1
ping 192.168.20.1
ping 192.168.30.1
```

Use the correct gateway for the department first. Then test the other VLAN gateways to prove inter-VLAN routing.

### Step 12.5 - Check interdepartment routing

From MKT-PC01:

```text
ping SALES-PC01-IP
ping SUP-PC01-IP
```

From SALES-PC01:

```text
ping MKT-PC01-IP
ping SUP-PC01-IP
```

Expected result: successful replies. This proves users can communicate across VLANs where allowed.

### Step 12.6 - Check DNS and intranet

From one department PC:

```text
ping intranet.mss.local
```

Then open Web Browser and browse to:

```text
http://intranet.mss.local
```

Expected result: the MSS Ltd intranet page loads by DNS name.

### Step 12.7 - Check database access

From one PC in Marketing, Sales and Support, browse to:

```text
http://marketing-db.mss.local
http://sales-db.mss.local
http://support-db.mss.local
```

Expected result: all three department database portals load from every department.

### Step 12.8 - Check backup protection

From a normal user PC:

```text
ping 192.168.60.10
```

Expected result: failure.

From ADMIN-PC:

```text
ping 192.168.60.10
```

Expected result: success.

On the core switches:

```cisco
show access-lists
show ip interface vlan 60
```

Expected result: ACL counters increase and VLAN 60 shows the ACL applied outbound.

### Step 12.9 - Check e-mail

1. Send an e-mail from marketing1@mss.local to sales1@mss.local.
2. Receive it on SALES-PC01.
3. Reply from sales1@mss.local to support1@mss.local.
4. Receive it on SUP-PC01.

Expected result: messages are delivered through MAIL-SRV.

### Step 12.10 - Check Internet and NAT

From a department PC:

```text
ping 198.51.100.10
```

Then browse to:

```text
http://www.test.net
```

On EDGE-R1:

```cisco
show ip nat translations
show ip nat statistics
```

Expected result:

- External web page loads.
- NAT translations appear on EDGE-R1.

### Step 12.11 - Check SSH management restriction

From ADMIN-PC:

```text
ssh -l netadmin 192.168.50.4
```

Expected result: SSH login prompt appears for SW-MKT.

From MKT-PC01, try the same command.

Expected result: connection fails or times out because only VLAN 50 is allowed to manage network devices.

---

## 13. Redundancy Testing

### Step 13.1 - Test access uplink redundancy

1. Start a continuous ping from MKT-PC01 to intranet.mss.local.
2. Shut down or disconnect the active uplink from SW-MKT to one core switch.
3. Wait for STP/HSRP reconvergence.
4. Confirm pings resume.
5. Reconnect the link.
6. Repeat for Sales, Support and Server switch uplinks.

Expected result: a few dropped pings are acceptable, but connectivity must recover automatically.

### Step 13.2 - Test core gateway redundancy

1. Run this on both core switches:

```cisco
show standby brief
```

2. Identify the active core for VLAN 10.
3. Temporarily shut the active VLAN 10 SVI or power off the active core switch.
4. Continue pinging 192.168.10.1 from MKT-PC01.
5. Confirm CORE-SW2 or CORE-SW1 becomes active for the HSRP group.
6. Restore the core switch.
7. Run:

```cisco
show standby brief
show spanning-tree
```

Expected result: gateway responsibility moves to the standby core and traffic recovers.

### Step 13.3 - Test edge route resilience

1. Confirm EDGE-R1 has primary and floating routes:

```cisco
show ip route
```

2. Shut down the preferred core-edge link for one VLAN path.
3. Test Internet access again.
4. Restore the link.

Expected result: backup route with administrative distance 5 is used if the primary path fails.

---

## 14. Troubleshooting Order

Use this order whenever something fails:

1. Cable - Check the cable is connected to the intended port.
2. Interface - Run `show interfaces status`.
3. VLAN - Run `show vlan brief`.
4. Trunk - Run `show interfaces trunk`.
5. SVI - Run `show ip interface brief`.
6. DHCP - Check DHCP pool, helper address, server gateway and VLAN.
7. Gateway - Confirm the PC uses the HSRP `.1` gateway.
8. Routing - Run `show ip route` on core switches and routers.
9. ACL - Run `show access-lists`.
10. NAT - Run `show ip nat translations` and `show ip nat statistics`.
11. DNS - Ping by IP first, then ping by DNS name.
12. STP/HSRP - Run `show spanning-tree` and `show standby brief`.

Common fixes:

| Symptom | Likely cause | Fix |
|---|---|---|
| PC gets 169.254.x.x | DHCP cannot reach the PC | Check access VLAN, trunks, SVI and helper address |
| SVI is down/down | No active port in that VLAN | Check VLAN membership and trunk status |
| One VLAN fails over a trunk | VLAN missing from allowed list | Add VLAN on both trunk ends |
| DNS name fails but IP works | DNS record or DNS server issue | Check INFRA-SRV DNS and client DNS address |
| NAT table is empty | NAT ACL/interface/default route issue | Check inside/outside roles, ACL 1 and routing |
| User can reach backup server | ACL missing or wrong direction | Apply BACKUP-PROTECTION outbound on VLAN 60 |
| SSH fails from Admin PC | SSH setup incomplete | Check domain name, RSA key, local user and VTY config |
| Redundant link is amber | STP blocking duplicate path | Normal behavior; do not force it forwarding |

---

## 15. Evidence To Capture

Capture clear screenshots in this order:

1. Full logical topology with labels.
2. Physical/floor layout.
3. VLAN table from core/access switch.
4. Trunk status.
5. IP interface brief from both core switches.
6. HSRP active/standby status.
7. STP status.
8. DHCP result from one PC per department.
9. DHCP pool and DNS records on INFRA-SRV.
10. Intranet page loaded by name.
11. Database pages loaded from employee PCs.
12. E-mail sent and received between departments.
13. Failed normal-user backup access.
14. Successful Admin backup access.
15. Internet page and NAT translation table.
16. Successful Admin SSH and blocked normal-user SSH.
17. Redundancy test before, during and after failure.
18. Port security and unused port shutdown evidence.
19. Final saved Packet Tracer topology.

Each screenshot caption should state:

- What was tested.
- Expected result.
- Actual result.
- Why the result matters to MSS Ltd.

---

## 16. Final Completion Checklist

Before submitting, confirm:

- [ ] Packet Tracer file opens without errors.
- [ ] 20 Marketing PCs are present.
- [ ] 25 Sales PCs are present.
- [ ] 15 Support PCs are present.
- [ ] Admin PC is in VLAN 50.
- [ ] VLANs 10, 20, 30, 40, 50, 60 and 99 exist.
- [ ] User ports are in the correct VLANs.
- [ ] Trunks allow the required VLANs.
- [ ] Native VLAN is 99.
- [ ] Unused ports are shut down and assigned to VLAN 99.
- [ ] SVIs are up/up on both core switches.
- [ ] HSRP gateways use the `.1` address in each VLAN.
- [ ] DHCP works for Marketing, Sales, Support and Management.
- [ ] Servers use static IP addresses.
- [ ] DNS records resolve correctly.
- [ ] Intranet works by DNS name.
- [ ] E-mail works between departments.
- [ ] All employees can access all three departmental database portals.
- [ ] Normal users cannot access backup VLAN services directly.
- [ ] Admin PC can access backup VLAN services.
- [ ] Internet access works through NAT.
- [ ] EDGE-R1 shows NAT translations.
- [ ] SSH management works from VLAN 50 only.
- [ ] Port security is enabled on user ports.
- [ ] STP prevents loops.
- [ ] Redundant links recover after failure.
- [ ] All configurations are saved with `write memory`.
- [ ] Evidence screenshots are readable and captioned.

---

## 17. Report Notes To Include

Use these points in the written coursework report:

- The design uses a hierarchical star topology because MSS Ltd has three floors and needs clear separation between departments, servers and the Internet edge.
- VLANs reduce broadcast traffic and separate Marketing, Sales, Support, servers, backup and management functions.
- Sales uses a /26 subnet and two switches because it has 25 users and needs more future growth space.
- HSRP gives users a stable default gateway even if one core switch fails.
- Rapid PVST+ prevents loops while still allowing redundant physical paths.
- NAT overload allows all internal private users to reach the Internet through EDGE-R1.
- The backup VLAN is intentionally protected from normal user VLANs.
- The database servers remain reachable from all departments because the brief requires company-wide database access.
- In production, real database authentication, role-based access control, HTTPS, secure mail, central AAA, firewall inspection, IDS/IPS, syslog, NTP and vulnerability scanning should be added.
- Packet Tracer is a simulation. It can show network reachability and services, but it does not provide real enterprise SQL databases, real backup scheduling, real firewall inspection or full vulnerability scanning.

---

## 18. Suggested Build Schedule

| Session | Work |
|---:|---|
| 1 | Place, rename and cable every device |
| 2 | Configure VLANs, trunks and access ports |
| 3 | Configure core SVIs, HSRP, STP and routing |
| 4 | Configure DHCP, DNS, intranet, database pages and e-mail |
| 5 | Configure EDGE-R1, ISP-R1, NAT and Internet server |
| 6 | Apply SSH restrictions, port security and backup ACL |
| 7 | Run functional, security and redundancy tests |
| 8 | Capture evidence and write the report |

Save versions as you build, for example:

- `MSS_01_Cabled.pkt`
- `MSS_02_VLANs.pkt`
- `MSS_03_Core_Routing.pkt`
- `MSS_04_Services.pkt`
- `MSS_05_Security.pkt`
- `MSS_06_Final_Tested.pkt`

