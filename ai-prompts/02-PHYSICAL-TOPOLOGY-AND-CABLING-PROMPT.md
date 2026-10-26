# Prompt 02: Physical Topology Placement & Cabling Matrix

Use this prompt to have an AI generate the exact step-by-step physical placement, renaming instructions, and cable patching guide for Cisco Packet Tracer.

---

```markdown
Provide a comprehensive, click-by-click physical placement and cabling guide for the MSS Ltd Cisco Packet Tracer network.

### Requirements:
1. Inventory and Models:
   - 2x Cisco 2911 Routers (named `ISP-R1` and `EDGE-R1`)
   - 1x Cisco ASA 5505 Firewall (named `FW-ASA1`)
   - 2x Cisco Catalyst 3560-24PS Multilayer Core Switches (named `CORE-SW1` and `CORE-SW2`)
   - 5x Cisco Catalyst 2960-24TT Access Switches (named `SW-MKT`, `SW-SALES-A`, `SW-SALES-B`, `SW-SUPPORT`, `SW-SERVER`)
   - 9x Servers (INTERNET-SRV, INFRA-SRV, MAIL-SRV, INTRANET-SRV, MKT-DB-SRV, SALES-DB-SRV, SUPPORT-DB-SRV, BACKUP-DB-SRV, ARCHIVE-SRV)
   - 61x Endpoints (20 Marketing PCs, 25 Sales PCs, 15 Support PCs, 1 Admin PC)

2. Cabling Specifications:
   - Provide an exhaustive, markdown table specifying every cable connection:
     * Source Device
     * Source Port
     * Target Device
     * Target Port
     * Cable Type (Copper Straight-Through or Crossover)
     * Logical Subnet & Role (WAN link, Outside transit, Inside transit, Core trunk, Access uplink, End-user access)
   - Include:
     * Internet Server to ISP
     * ISP to EDGE
     * EDGE to ASA Outside
     * ASA Inside to CORE-SW1 and CORE-SW2
     * Inter-Core Trunk between CORE-SW1 and CORE-SW2
     * Dual uplinks from each access switch (uplink to CORE-SW1 and uplink to CORE-SW2)
     * Port allocations on SW-SERVER for all 8 internal servers and ADMIN-PC

3. Layout Advice:
   - Describe the recommended visual layout in Packet Tracer (Top-to-Bottom hierarchy) so cables do not overlap unnecessarily and the topology looks professional for coursework documentation and screenshots.
```

