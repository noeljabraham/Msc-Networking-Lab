# Prompt 06: Server Farm Services & Endpoint Deployment

Use this prompt to generate instructions for configuring Packet Tracer server services (DHCP pools, DNS A-records, HTTP websites, Email) and department client PCs.

---

```markdown
Provide the complete server service configuration and endpoint deployment guide for the MSS Ltd Cisco Packet Tracer lab.

### Requirements:

1. Server Farm Setup (SW-SERVER - VLAN 40, 50, 60):
   Provide exact IP, Subnet Mask, Default Gateway, and GUI service settings for each server:
   - `INFRA-SRV` (`192.168.40.10/27`, Gateway `192.168.40.1`):
     * DHCP Service: Enable and create 3 distinct pools:
       1. `MARKETING_POOL`: Default Gateway `192.168.10.1`, DNS `192.168.40.10`, Start IP `192.168.10.10`, Mask `255.255.255.224`, Max Users `20`.
       2. `SALES_POOL`: Default Gateway `192.168.20.1`, DNS `192.168.40.10`, Start IP `192.168.20.10`, Mask `255.255.255.192`, Max Users `30`.
       3. `SUPPORT_POOL`: Default Gateway `192.168.30.1`, DNS `192.168.40.10`, Start IP `192.168.30.10`, Mask `255.255.255.224`, Max Users `20`.
     * DNS Service: Enable and add A-records:
       - `intranet.mss.local` -> `192.168.40.12`
       - `mail.mss.local` -> `192.168.40.11`
       - `mkt-db.mss.local` -> `192.168.40.20`
       - `sales-db.mss.local` -> `192.168.40.21`
       - `support-db.mss.local` -> `192.168.40.22`
   - `INTRANET-SRV` (`192.168.40.12/27`):
     * HTTP Service: Edit `index.html` to display a branded MSS Ltd Intranet portal welcoming employees.
   - `MAIL-SRV` (`192.168.40.11/27`):
     * EMAIL Service: Domain `mss.local`, configure test user accounts (`mkt01`, `sales01`, `support01`, `admin`).
   - Department Database Portals (`MKT-DB-SRV .20`, `SALES-DB-SRV .21`, `SUPPORT-DB-SRV .22`):
     * HTTP Services with custom web pages simulating departmental data access portals.
   - Backup Servers (VLAN 60):
     * `BACKUP-DB-SRV` (`192.168.60.10/28`, Gateway `192.168.60.1`)
     * `ARCHIVE-SRV` (`192.168.60.11/28`, Gateway `192.168.60.1`)
   - Management Workstation (VLAN 50):
     * `ADMIN-PC` (`192.168.50.10/28`, Gateway `192.168.50.1`, DNS `192.168.40.10`)

2. Department PC Deployment:
   - Provide step-by-step instructions for switching PCs (`MKT-PC01` to `20`, `SALES-PC01` to `25`, `SUP-PC01` to `15`) from Static to DHCP.
   - Explain how to verify IP lease acquisition using the PC Desktop Command Prompt (`ipconfig /all`).
   - Provide quick sanity check scripts for testing gateway reachability, DNS lookup, and HTTP web browsing.
```

