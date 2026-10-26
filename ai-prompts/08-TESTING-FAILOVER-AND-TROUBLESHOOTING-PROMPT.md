# Prompt 08: End-to-End Verification, Redundancy Testing & Troubleshooting

Use this prompt to generate the test matrix, failover drill procedures, screenshot capture checklist, and troubleshooting decision tree for the final coursework report.

---

```markdown
Generate a comprehensive, end-to-end verification, high-availability redundancy failover drill, and troubleshooting guide for the MSS Ltd network.

### Deliverables Required:

1. Functional Testing Matrix:
   - Provide a markdown test table with columns:
     * Test Case ID
     * Description & Purpose
     * Source Device
     * Target / Destination
     * Expected Result
     * Verification Command / GUI Action
     * Pass / Fail Criteria
   - Include test cases for:
     * DHCP IP assignment across all 3 floors
     * Ping default gateway (HSRP virtual IP 192.168.x.1)
     * Inter-VLAN routing (Marketing PC pinging Sales PC)
     * Internal DNS lookup (`nslookup intranet.mss.local`)
     * HTTP web access to company intranet and departmental database portals
     * External Internet access to `198.51.100.10` through the ASA firewall and EDGE NAT overload
     * Email sending and retrieval between test accounts
     * ACL enforcement: Verify Marketing PC receives "Destination Host Unreachable" or request timed out when pinging Backup Server `192.168.60.10`
     * Admin PC access: Verify `ADMIN-PC` in VLAN 50 CAN access `192.168.60.10`

2. High-Availability (HSRP & STP) Redundancy Failover Drill:
   - Step-by-step procedure to prove the dual-core redundancy:
     * Step 1: Open command prompt on `MKT-PC01` and initiate continuous ping: `ping -t 198.51.100.10`.
     * Step 2: On `CORE-SW1`, execute `show standby brief` to confirm it is `Active`.
     * Step 3: Administratively disable the transit interface on `CORE-SW1` (`interface FastEthernet0/24` -> `shutdown`) or shut down the switch.
     * Step 4: Observe the ping window and record how many packet drops occur before `CORE-SW2` transitions from `Standby` to `Active`.
     * Step 5: On `CORE-SW2`, verify takeover with `show standby brief`.
     * Step 6: Bring `CORE-SW1` back up (`no shutdown`) and verify `preempt` restores `CORE-SW1` as the active gateway without disrupting active user sessions.

3. Evidence & Screenshot Capture Checklist:
   - Specific list of screenshots required for the university coursework report to guarantee full marks:
     * Network topology with all green link lights
     * `show standby brief` showing active/standby states on both core switches
     * `show ip route` on both core switches
     * `show ip nat translations` on EDGE-R1 displaying active inside-global to inside-local mappings
     * `show conn` on FW-ASA1 displaying active inspected sessions
     * Web browser displaying the intranet website from a client PC
     * Web browser displaying the external Internet website from a client PC
     * Ping denial proof showing ACL blocking unauthorized access to VLAN 60

4. Troubleshooting Decision Flowchart & Common Packet Tracer Traps:
   - Provide solutions for common issues:
     * DHCP client shows `169.254.x.x` (APIPA) -> Missing `ip helper-address` on SVI or trunk mismatch.
     * Internet ping fails from PC -> Missing default route on core, missing route on ASA, or missing NAT overload ACL on EDGE-R1.
     * HSRP duplicate IP warning -> Native VLAN mismatch or trunk failure.
     * Red link on ASA port -> ASA port disabled or speed/duplex mismatch.
```

