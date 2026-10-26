# Prompt 03: VLAN Design & IP Addressing Scheme

Use this prompt to have an AI generate the complete IP addressing scheme, subnetting calculations, CIDR masks, HSRP gateways, and DHCP pool configurations for MSS Ltd.

---

```markdown
Design the complete VLAN and IP addressing scheme for the MSS Ltd campus network.

### Design Criteria:
1. Department Requirements:
   - Marketing (Floor 1): 20 hosts required + 20% growth.
   - Sales (Floor 2): 25 hosts required + 20% growth.
   - Support (Floor 3): 15 hosts required + 20% growth.
   - Production Servers (Server Room): DNS, DHCP, Email, Intranet, 3x Department DBs (~8 hosts).
   - Management: Network switch management interfaces + Admin PC.
   - Backup: Isolated backup database and archive server (~4 hosts).
   - Transit Networks:
     * FW-to-Core transit network (accommodating ASA inside, CORE-SW1, CORE-SW2, and HSRP virtual IP).
     * FW-to-EDGE transit network.
     * EDGE-to-ISP WAN network.
     * Public Internet Server network.

2. What You Must Output:
   - A master markdown table detailing:
     * VLAN ID & VLAN Name
     * Subnet Address in CIDR notation
     * Subnet Mask (decimal)
     * Usable Host IP Range
     * Broadcast Address
     * HSRP Virtual Gateway IP (the default gateway handed out to endpoints)
     * CORE-SW1 Physical SVI IP (Priority 110, Preempt)
     * CORE-SW2 Physical SVI IP (Priority 100)
     * Static Scope vs DHCP Dynamic Scope
   - Subnetting justification explaining why specific prefix lengths (/27, /26, /28, /29, /30) were chosen for each subnet to prevent IPv4 address waste while accommodating future growth.
   - SVI and DHCP Scope definition table (Pool Name, Network, Default Router, DNS Server, Excluded Address ranges).
```

