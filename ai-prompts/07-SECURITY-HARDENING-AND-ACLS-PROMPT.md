# Prompt 07: Security Hardening, Port Security & Access Control Lists

Use this prompt to generate switchport security hardening commands, SSH configuration, VTY protection, and extended Access Control Lists (ACLs) to protect the backup database.

---

```markdown
Generate the comprehensive security hardening and access control implementation for the MSS Ltd Cisco network.

### Security Domains to Implement:

1. Switchport Port-Security (Layer 2 Access Layer):
   - Device: All 5 Access Switches (`SW-MKT`, `SW-SALES-A`, `SW-SALES-B`, `SW-SUPPORT`, `SW-SERVER`).
   - Configuration requirements for user access ports:
     * `switchport mode access`
     * `switchport port-security`
     * `switchport port-security maximum 2` (permits 1 host + 1 test machine/phone)
     * `switchport port-security violation restrict` (drops packets and increments violation counter without dropping legitimate traffic)
     * `switchport port-security mac-address sticky` (dynamically learns and commits connected MAC addresses to running-config)

2. Administrative & Management Hardening:
   - Devices: `CORE-SW1`, `CORE-SW2`, and all access switches.
   - Global Settings:
     * `ip domain-name mss.local`
     * `crypto key generate rsa modulus 1024` (or 2048)
     * `username admin privilege 15 secret CiscoAdmin@MSS2026`
     * `enable secret CiscoSecret@MSS2026`
     * `service password-encryption`
     * Banner MOTD with authorized access legal warning.
   - VTY Line Security:
     * `line vty 0 4` (or `0 15`)
     * `transport input ssh` (explicitly disables unencrypted Telnet)
     * `login local`
     * `exec-timeout 5 0`

3. Department Isolation & Backup Database Protection ACLs:
   - Location: Apply extended Access Control Lists on the Multilayer Core Switches (`CORE-SW1` and `CORE-SW2`).
   - Policy Requirements:
     * Rule 1: The isolated Backup Database and Archive Server in VLAN 60 (`192.168.60.0/28`) MUST ONLY be accessible by the `ADMIN-PC` in VLAN 50 (`192.168.50.0/28`) and automated backup web replication from production servers (`192.168.40.0/27`).
     * Rule 2: General employees in Marketing (VLAN 10), Sales (VLAN 20), and Support (VLAN 30) MUST BE DENIED access to VLAN 60.
     * Rule 3: All departments must retain unrestricted access to internal shared services (DNS, DHCP, Mail, Intranet web, and their departmental DB portals in VLAN 40).
     * Rule 4: Normal outbound Internet access must remain permitted.
   - Provide the complete named extended ACL script (`BACKUP_PROTECT`) and the exact interface command (`ip access-group ... in/out`) to apply it on SVI Vlan60 or Vlan10/20/30.

4. Unused Ports & Native VLAN Hardening:
   - Script to move all unallocated switchports across every switch into `VLAN 99` (`NATIVE-BLACKHOLE`), disable CDP, and administratively shut them down (`shutdown`).
```

