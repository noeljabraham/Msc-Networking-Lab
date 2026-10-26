# Prompt 04: Core Multilayer & Access Switching Configurations

Use this prompt to generate complete, ready-to-paste Cisco IOS configurations for both multilayer core switches and all access switches.

---

```markdown
Generate the complete, production-ready Cisco IOS CLI configuration scripts for the switching infrastructure of MSS Ltd in Cisco Packet Tracer.

### Devices to Configure:
1. `CORE-SW1` (Cisco 3560-24PS Multilayer Switch - Primary Core)
2. `CORE-SW2` (Cisco 3560-24PS Multilayer Switch - Secondary Core)
3. `SW-MKT` (Cisco 2960-24TT - Marketing Floor 1)
4. `SW-SALES-A` (Cisco 2960-24TT - Sales Floor 2, Users 01-24)
5. `SW-SALES-B` (Cisco 2960-24TT - Sales Floor 2, User 25 & Spare Capacity)
6. `SW-SUPPORT` (Cisco 2960-24TT - Support Floor 3)
7. `SW-SERVER` (Cisco 2960-24TT - Server Room)

### Technical Specifications & Features Required:
1. Multilayer Core Switches (CORE-SW1 and CORE-SW2):
   - Global: Hostname, `ip routing`, Rapid-PVST+ (`spanning-tree mode rapid-pvst`).
   - VLAN Database: VLANs 10, 20, 30, 40, 50, 60, 99 created and named.
   - Inter-Core Trunk (Gi0/1): `switchport trunk encapsulation dot1q`, `switchport mode trunk`, `switchport trunk native vlan 99`, allowed VLANs 10,20,30,40,50,60,99.
   - Access Uplink Trunks (Fa0/1 to Fa0/5): 802.1Q trunking with native VLAN 99.
   - Transit Link to ASA Firewall (Fa0/24): Configured on transit subnet `10.255.0.0/29`.
   - SVIs for VLANs 10, 20, 30, 40, 50, 60:
     * CORE-SW1: Physical IP `.2`, HSRP Priority `110`, `preempt`, HSRP Virtual IP `.1`.
     * CORE-SW2: Physical IP `.3`, HSRP Priority `100`, HSRP Virtual IP `.1`.
     * Transit SVI/port: HSRP Virtual IP `10.255.0.4/29` with CORE-SW1 as primary.
   - DHCP Relay: `ip helper-address 192.168.40.10` on departmental SVIs (VLAN 10, 20, 30).
   - Default Route: `ip route 0.0.0.0 0.0.0.0 10.255.0.1` pointing to the ASA inside interface.
   - Spanning Tree Priorities:
     * CORE-SW1: `root primary` for all VLANs.
     * CORE-SW2: `root secondary` for all VLANs.

2. Access Switches (`SW-MKT`, `SW-SALES-A`, `SW-SALES-B`, `SW-SUPPORT`, `SW-SERVER`):
   - Hostname and VLAN database definitions.
   - Dual Uplink Trunks (Gi0/1 to CORE-SW1, Gi0/2 to CORE-SW2) with 802.1Q trunk mode and native VLAN 99.
   - User Access Ports:
     * Mode: `switchport mode access`
     * Access VLAN assignment corresponding to the department.
     * `spanning-tree portfast` enabled.
     * `spanning-tree bpduguard enable` enabled.
   - Management SVI on VLAN 50 with IP address, subnet mask, and `ip default-gateway 192.168.50.1`.
   - Security: Shutdown all unused ports and assign them to native VLAN 99.

Format every device configuration in a standalone code block that can be directly pasted into the Packet Tracer CLI.
```

