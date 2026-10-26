# Prompt 05: Perimeter Firewall (ASA 5505) & Edge NAT Configuration

Use this prompt to generate the configurations for the Cisco ASA 5505 perimeter firewall, the company edge router (NAT Overload), and the external ISP router.

---

```markdown
Generate the complete CLI configurations for the perimeter security and Internet boundary devices in the MSS Ltd network.

### Devices to Configure:
1. `FW-ASA1` (Cisco ASA 5505 Stateful Firewall)
2. `EDGE-R1` (Cisco 2911 Company Edge Router)
3. `ISP-R1` (Cisco 2911 Simulated ISP Router)
4. `INTERNET-SRV` (External Simulated Server IP and DNS/HTTP services)

### Technical Requirements:

1. `FW-ASA1` (ASA 5505):
   - Inside Zone:
     * Interface: `Vlan1`
     * Nameif: `inside`
     * Security Level: `100`
     * IP Address: `10.255.0.1 255.255.255.248`
     * Physical Ports: `Ethernet0/1` and `Ethernet0/2` assigned to `switchport access vlan 1`.
   - Outside Zone:
     * Interface: `Vlan2`
     * Nameif: `outside`
     * Security Level: `0`
     * IP Address: `10.255.1.2 255.255.255.252`
     * Physical Port: `Ethernet0/0` assigned to `switchport access vlan 2`.
   - Inspection Policy:
     * Enable stateful inspection for `icmp`, `http`, and `dns` under `global_policy`.
   - Routing:
     * Static route directing all internal company traffic (`192.168.0.0 255.255.0.0`) back to the HSRP virtual transit IP `10.255.0.4`.
     * Default route (`0.0.0.0 0.0.0.0`) pointing out to EDGE-R1 (`10.255.1.1`).
   - Perimeter Access List:
     * Outside ACL applied to the outside interface denying inbound unsolicited traffic from the external world while permitting return traffic inspected statefully.

2. `EDGE-R1` (Company Edge Router):
   - Interfaces:
     * `GigabitEthernet0/0` (facing ASA outside): `10.255.1.1 255.255.255.252`, `ip nat inside`.
     * `GigabitEthernet0/2` (facing ISP-R1 WAN): `203.0.113.2 255.255.255.252`, `ip nat outside`.
   - NAT Overload (PAT) Implementation:
     * Standard Access List 1 permitting internal network ranges (`192.168.0.0 0.0.255.255`).
     * `ip nat inside source list 1 interface GigabitEthernet0/2 overload`.
   - Routing:
     * Static route for `192.168.0.0 255.255.0.0` pointing to ASA outside IP `10.255.1.2`.
     * Default route `0.0.0.0 0.0.0.0` pointing to ISP-R1 `203.0.113.1`.

3. `ISP-R1` (ISP Router):
   - Interfaces:
     * `GigabitEthernet0/0` (facing EDGE-R1): `203.0.113.1 255.255.255.252`.
     * `GigabitEthernet0/1` (facing INTERNET-SRV): `198.51.100.1 255.255.255.0`.
   - No NAT or firewalling; simple IP forwarder simulating the public Internet backbone.

Provide the exact CLI commands for each device, along with verification commands to inspect NAT tables (`show ip nat translations`) and ASA state tables (`show conn`).
```

