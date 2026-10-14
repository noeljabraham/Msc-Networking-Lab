# MSS Ltd Vulnerability Analysis - Complete Implementation Guide

This guide explains how to perform, record, and write the 30% vulnerability analysis component for the MSS Ltd network defined in `implementation-steps.md`.

It is specific to this topology:

- Marketing: VLAN 10, `192.168.10.0/27`, 20 workstations.
- Sales: VLAN 20, `192.168.20.0/26`, 25 workstations.
- General Support: VLAN 30, `192.168.30.0/27`, 15 workstations.
- Production servers: VLAN 40, `192.168.40.0/27`.
- Management: VLAN 50, `192.168.50.0/28`.
- Backup and archive: VLAN 60, `192.168.60.0/28`.
- Native/black-hole VLAN: VLAN 99.
- Internet edge: `EDGE-R1`, NAT, `ISP-R1`, and `INTERNET-SRV`.
- Redundancy: two multilayer core switches, HSRP, Rapid PVST+, and dual access uplinks.

The coursework brief gives this component the following marks:

| Required part | Weight |
|---|---:|
| Security vulnerabilities in the given setup | 20% |
| Suitable Kali Linux or Windows tools, with implementation examples | 40% |
| Defences based on the findings | 30% |
| Regular security and vulnerability-maintenance guideline | 10% |

The final coursework report must not exceed 20 pages, so place detailed commands, full outputs, and extra screenshots in an appendix. Keep the main vulnerability section focused on findings, evidence, risk, and improvements.

---

## 1. Rules, Authorisation, And Safety

Only test systems owned by the group or explicitly provided for the coursework. Do not scan the university network, home router, public websites, or any system outside the isolated lab.

Before testing, complete this scope record:

| Scope item | Agreed value |
|---|---|
| Project | MSS Ltd coursework vulnerability assessment |
| Authorised testers | Group member names |
| Authorising person | Tutor/lab owner as applicable |
| Test dates and times | Record before starting |
| In-scope networks | Lab copies of `192.168.10.0/27`, `192.168.20.0/26`, `192.168.30.0/27`, `192.168.40.0/27`, `192.168.50.0/28`, and `192.168.60.0/28` |
| In-scope hosts | Only group-owned lab hosts listed in the asset register |
| Out-of-scope systems | University, public, personal, and production networks |
| Permitted tests | Discovery, configuration review, safe service scanning, packet capture, authenticated vulnerability scanning, and approved validation |
| Prohibited tests | Denial of service, destructive payloads, persistence, data deletion, uncontrolled phishing, and testing third-party systems |
| Stop conditions | Unexpected outage, data corruption, scope uncertainty, or traffic leaving the lab |
| Recovery | VM snapshots, saved Packet Tracer file, and exported device configurations |

Use these operating rules:

1. Work on a clone named `MSS_VULN_LAB`, not the only copy of the final Packet Tracer file.
2. Take VM snapshots before every active test.
3. Use host-only or internal virtual networking for Kali and target VMs.
4. Disable bridged networking while conducting active tests.
5. Start with passive review, then low-impact discovery, then authenticated scanning, and only then approved validation.
6. Use low scan rates and test one server at a time.
7. Stop immediately if a target becomes unstable.
8. Never include real passwords, private data, session cookies, or full sensitive packet contents in the report.
9. Record every command, time, source address, target, and result.
10. Restore the clean snapshot after any exploitation demonstration.

---

## 2. Understand What Packet Tracer Can And Cannot Prove

Packet Tracer is suitable for proving:

- VLAN separation and trunk configuration.
- HSRP, STP, routing, DHCP, DNS, NAT, ACL, and basic service reachability.
- Successful and blocked traffic paths.
- SSH-only network management.
- Port security, BPDU Guard, and disabled unused ports.
- Packet movement in Simulation mode.

Packet Tracer is not a real operating system or vulnerability-testing target. It cannot provide a realistic result from Nmap, OpenVAS/Nessus, Nikto, Hydra, Wireshark, or Metasploit. Its HTTP database pages are simulations, not SQL databases.

Use two complementary test environments:

| Environment | Purpose | Evidence |
|---|---|---|
| Packet Tracer copy | Validate topology, segmentation, ACLs, management restriction, and redundancy | CLI output, successful/failed tests, Simulation-mode PDUs |
| Isolated virtual lab | Demonstrate real security tools against deliberately created lab services | Terminal output, scan reports, packet captures, remediation and retest |

Do not claim that a Kali scan was run directly against Packet Tracer unless a real external integration has actually been built and verified.

---

## 3. Prepare The Evidence Workspace

Create this folder structure outside Packet Tracer:

```text
MSS_Vulnerability_Evidence/
  00_authorisation/
  01_asset_and_scope/
  02_packet_tracer_baseline/
  03_discovery/
  04_packet_capture/
  05_vulnerability_scans/
  06_web_tests/
  07_password_audit/
  08_validation/
  09_defences/
  10_retest/
  11_report_figures/
```

Use a consistent evidence name:

```text
YYYYMMDD-HHMM_tool_source_target_test.ext
```

Example:

```text
20261020-1430_nmap_kali_intranet_service-scan.txt
```

Create an evidence log:

| ID | Date/time | Tester | Source | Target | Tool/command | Expected | Actual | Screenshot/file | Finding ID |
|---|---|---|---|---|---|---|---|---|---|
| EV-001 | | | | | | | | | |

Create a finding register:

| ID | Asset | Vulnerability | Evidence | Likelihood | Impact | Risk | Recommended defence | Retest result |
|---|---|---|---|---:|---:|---:|---|---|---|
| F-01 | | | | | | | | |

Before each screenshot, show enough context to identify the tool and target but hide passwords, tokens, and unrelated personal information.

---

## 4. Build The Test Asset Register

Record the expected assets before scanning. This prevents an expected service from being reported as an unknown compromise and makes rogue devices easier to identify.

| Asset | Address | VLAN/network | Expected role | Expected exposure |
|---|---|---|---|---|
| `CORE-SW1` | `192.168.50.2` | VLAN 50 | Core, HSRP, routing | SSH from VLAN 50 only |
| `CORE-SW2` | `192.168.50.3` | VLAN 50 | Core, HSRP, routing | SSH from VLAN 50 only |
| `SW-MKT` | `192.168.50.4` | VLAN 50 | Access switch | SSH from VLAN 50 only |
| `SW-SALES-A` | `192.168.50.5` | VLAN 50 | Access switch | SSH from VLAN 50 only |
| `SW-SALES-B` | `192.168.50.6` | VLAN 50 | Access switch | SSH from VLAN 50 only |
| `SW-SUPPORT` | `192.168.50.7` | VLAN 50 | Access switch | SSH from VLAN 50 only |
| `SW-SERVER` | `192.168.50.8` | VLAN 50 | Server access switch | SSH from VLAN 50 only |
| `INFRA-SRV` | `192.168.40.10` | VLAN 40 | DHCP and DNS | Required DHCP/DNS only |
| `MAIL-SRV` | `192.168.40.11` | VLAN 40 | SMTP and POP3 | Required mail services only |
| `INTRANET-SRV` | `192.168.40.12` | VLAN 40 | Intranet | HTTP/HTTPS as configured |
| `MKT-DB-SRV` | `192.168.40.20` | VLAN 40 | Simulated DB portal | Employee web access |
| `SALES-DB-SRV` | `192.168.40.21` | VLAN 40 | Simulated DB portal | Employee web access |
| `SUPPORT-DB-SRV` | `192.168.40.22` | VLAN 40 | Simulated DB portal | Employee web access |
| `BACKUP-DB-SRV` | `192.168.60.10` | VLAN 60 | Backup database | VLANs 40 and 50 only |
| `ARCHIVE-SRV` | `192.168.60.11` | VLAN 60 | FTP archive simulation | VLANs 40 and 50 only |
| `INTERNET-SRV` | `198.51.100.10` | External test LAN | Internet web test | HTTP test page |

For the virtual lab, assign only the minimum number of target VMs needed. A practical mapping is:

- Kali Linux VM: authorised test workstation.
- Windows Server VM with IIS: represents `INTRANET-SRV`.
- Optional second Windows Server VM: represents a database portal or archive service.
- Optional Windows client VM: represents an employee endpoint.

Use the same logical roles, but record the real VM addresses separately if the hypervisor cannot reproduce the Packet Tracer subnets.

---

## 5. Risk Rating Method

Use a simple, consistent 5 x 5 model.

### 5.1 Likelihood

| Score | Meaning | MSS Ltd interpretation |
|---:|---|---|
| 1 | Rare | Requires exceptional access or conditions |
| 2 | Unlikely | Difficult and not normally exposed |
| 3 | Possible | Plausible with internal access or common mistakes |
| 4 | Likely | Common technique against an exposed or weak service |
| 5 | Almost certain | Easily repeated with little skill or already observed |

### 5.2 Impact

| Score | Meaning | MSS Ltd interpretation |
|---:|---|---|
| 1 | Negligible | No material service or data effect |
| 2 | Minor | Small, local, short-lived disruption |
| 3 | Moderate | One department or non-critical service affected |
| 4 | Major | Multiple departments, sensitive data, or extended outage |
| 5 | Severe | Company-wide loss, major breach, or unrecoverable data loss |

Calculate:

```text
Risk score = Likelihood x Impact
```

| Score | Rating | Response |
|---:|---|---|
| 1-4 | Low | Accept or improve during routine maintenance |
| 5-9 | Medium | Plan and track treatment |
| 10-16 | High | Prioritise remediation and management review |
| 17-25 | Critical | Stop exposure and remediate immediately |

State that the score is a reasoned coursework estimate, not an automatically generated fact. Adjust a score when evidence changes.

---

## 6. Initial MSS Ltd Threat And Vulnerability Register

This is the starting hypothesis. Confirm, reject, or revise each item through testing.

| ID | Asset/process | Vulnerability or threat | Why it applies here | L | I | Initial risk |
|---|---|---|---|---:|---:|---:|
| F-01 | Network devices | Example, shared, or weak administrator credentials | The implementation contains replacement placeholders and local accounts | 4 | 5 | 20 Critical |
| F-02 | Management plane | Management reachable outside VLAN 50 because of a missing/misapplied VTY ACL | Many devices must receive the same ACL manually | 3 | 5 | 15 High |
| F-03 | Mail and web traffic | HTTP, POP3, SMTP, and FTP are unencrypted in the Packet Tracer model | Credentials/content may be visible to a correctly positioned observer | 4 | 4 | 16 High |
| F-04 | Servers | Unnecessary or outdated services | Packet Tracer services are manually enabled; real servers require patching and hardening | 3 | 4 | 12 High |
| F-05 | Backup VLAN | ACL error exposes backup or archive systems to user VLANs | Backup security depends on an identical ACL on both cores | 3 | 5 | 15 High |
| F-06 | Department databases | All staff can reach all three portals | The brief requires reachability, but application authentication is not simulated | 4 | 4 | 16 High |
| F-07 | Access layer | Rogue endpoint, MAC flooding, or stolen wall-port access | Sixty employee ports exist across three floors | 3 | 4 | 12 High |
| F-08 | Layer 2 | ARP spoofing or DHCP spoofing | VLANs limit scope but do not authenticate ARP or DHCP | 3 | 4 | 12 High |
| F-09 | Trunks | VLAN hopping or native-VLAN inconsistency | Many manual trunks must consistently use VLAN 99 and an allowed list | 2 | 4 | 8 Medium |
| F-10 | Endpoints/e-mail | Phishing, malware, and credential theft | All employees use e-mail and Internet services | 4 | 5 | 20 Critical |
| F-11 | Visibility | No central AAA, syslog, SIEM, IDS/IPS, or time synchronisation | Packet Tracer design has limited detection and accountability | 4 | 4 | 16 High |
| F-12 | Availability | Single `EDGE-R1` and single ISP connection | Core redundancy does not remove the Internet-edge single points of failure | 3 | 4 | 12 High |
| F-13 | Servers | Individual service/server failure | DHCP/DNS, mail, intranet, and backup roles each rely on individual simulated systems | 3 | 4 | 12 High |
| F-14 | Backup process | Backup pages exist, but no real scheduled, immutable, or tested backup | Packet Tracer only simulates the service label | 4 | 5 | 20 Critical |
| F-15 | Physical infrastructure | Theft, power loss, fire, or unauthorised server-room access | Three floors and a server room require physical controls | 3 | 5 | 15 High |
| F-16 | Redundancy design | HSRP/STP configured but not monitored or regularly tested | Configuration alone does not prove recovery | 3 | 4 | 12 High |

Do not present every hypothesis as a confirmed vulnerability. A confirmed finding needs reproducible evidence.

---

## 7. Phase 1 - Packet Tracer Baseline And Configuration Audit

Save a copy of the completed network as:

```text
MSS_07_Pre_Vulnerability_Test.pkt
```

### 7.1 Collect device state

Run the following on both core switches:

```cisco
show running-config
show ip interface brief
show vlan brief
show interfaces trunk
show spanning-tree
show standby brief
show ip route
show access-lists
show ip interface vlan 60
show users
show ssh
```

Run on every access switch:

```cisco
show running-config
show vlan brief
show interfaces trunk
show interfaces status
show port-security
show port-security interface fastEthernet 0/1
show spanning-tree
show ip interface brief
```

Run on `EDGE-R1`:

```cisco
show running-config
show ip interface brief
show ip route
show access-lists
show ip nat statistics
show ip nat translations
show users
show ssh
```

Capture the output or screenshots. Check for:

- Default or replacement text still used as a password.
- Telnet permitted on a VTY line.
- HTTP management enabled unnecessarily.
- VTY ACL absent or applied to the wrong line range.
- VLAN 1 used for users, management, or trunk native traffic.
- VLAN 99 missing from a trunk or mismatched at one end.
- Extra VLANs in the trunk allowed list.
- Unused interfaces that are active.
- User access ports without port security, PortFast, or BPDU Guard.
- Backup ACL absent, in the wrong direction, or present on only one core.
- Unexpected static routes or NAT rules.
- Configuration changes not saved.

### 7.2 Prove management-plane restriction

From `ADMIN-PC` in VLAN 50:

```text
ssh -l netadmin 192.168.50.4
```

Expected: an SSH prompt from `SW-MKT`.

Repeat from `MKT-PC01` in VLAN 10.

Expected: connection fails or times out.

Test representative targets:

- One core switch.
- `EDGE-R1`.
- One access switch.

If a user PC can obtain an SSH prompt, record a high-risk finding. Save the exact source, target, result, and the relevant `show access-lists` output.

### 7.3 Prove backup segmentation

From `MKT-PC01`, `SALES-PC01`, and `SUP-PC01`:

```text
ping 192.168.60.10
ping 192.168.60.11
```

Expected: blocked.

From `ADMIN-PC`:

```text
ping 192.168.60.10
ping 192.168.60.11
```

Expected: successful.

From a production server in VLAN 40, verify the authorised backup path also works.

On both cores:

```cisco
show access-lists BACKUP-PROTECTION
show ip interface vlan 60
```

Expected: the ACL is outbound on VLAN 60 and the appropriate counters increase. A single successful ping does not prove every service is protected; state this limitation.

### 7.4 Check unused ports and rogue-device resistance

On each access switch:

```cisco
show interfaces status
show vlan brief
show port-security
```

Expected:

- Unused ports are assigned to VLAN 99.
- Unused ports show `disabled` or administratively down.
- Active user ports use one sticky secure MAC address.

For a controlled port-security demonstration:

1. Record the existing secure MAC on one test port.
2. Save the Packet Tracer file.
3. Disconnect the authorised test PC.
4. Connect a different test PC to the same port.
5. Generate one ping.
6. Run:

```cisco
show port-security interface fastEthernet 0/1
```

Expected: a violation counter increases and the restrict action drops unauthorised frames without shutting the port.

Restore the original PC and verify normal connectivity.

### 7.5 Inspect plain-text protocols in Simulation mode

Use Simulation mode to observe representative HTTP, FTP, SMTP/POP3, DNS, and ICMP flows.

1. Select **Simulation**.
2. Use **Edit Filters** to show only the protocol under test.
3. Generate one approved transaction.
4. Open the event/PDU details.
5. Record the protocol, source, destination, and whether confidentiality is provided.

Do not overstate what Packet Tracer displays. The defensible finding is that the design uses protocols that do not provide transport encryption, not that a real credential was definitely stolen.

### 7.6 Confirm resilience without treating it as security prevention

Run continuous pings and repeat the documented uplink and HSRP failure tests. Record:

- Time failure was introduced.
- Number of lost pings.
- Time traffic recovered.
- HSRP active/standby change.
- STP forwarding/blocking change.
- Whether Internet service survived a single core link failure.

Then document that failure of the only `EDGE-R1` or the only ISP connection still causes an outage.

---

## 8. Phase 2 - Build The Isolated Kali Test Lab

Packet Tracer's servers are simulated devices. They cannot be scanned directly by a real Kali VM. In this external lab, a Windows Server VM becomes the real test implementation of the Packet Tracer `INTRANET-SRV`.

```text
Packet Tracer design                 External vulnerability lab

MKT-PC01 or ADMIN-PC                 Kali Linux VM
        |                                  |
CORE-SW1 / CORE-SW2                 Isolated virtual network
        |                                  |
INTRANET-SRV                        Windows Server with IIS
192.168.40.12                       192.168.56.20 initially
```

The first lab uses a simple `192.168.56.0/24` subnet. The different address does not change the server's role. Later, the VM may be moved to `192.168.40.12/27` behind a virtual router to reproduce the MSS Ltd subnets more closely.

### 8.1 What Windows Server Represents

For the minimum coursework demonstration, use one Windows Server VM as:

```text
Packet Tracer device: INTRANET-SRV
Packet Tracer address: 192.168.40.12/27
External-lab implementation: Windows Server with IIS
Initial external-lab address: 192.168.56.20/24
```

This allows Kali to perform real Nmap, Nikto, Wireshark/TShark, and vulnerability-scanner tests against a real web service that represents the MSS Ltd intranet.

Windows Server can represent additional Packet Tracer servers later:

| Packet Tracer server | Windows Server role/application |
|---|---|
| `INTRANET-SRV` | IIS Web Server |
| `INFRA-SRV` | Windows DNS and DHCP roles |
| Department database server | IIS application and, if required, SQL Server Express |
| `ARCHIVE-SRV` | Windows file services or an authorised secure-transfer service |
| Central authentication proposal | Active Directory Domain Services and Group Policy |

Do not install every role for the first test. Begin with IIS only so that the exposed ports and findings are easy to interpret.

### 8.2 Recommended Minimum Lab

Use VirtualBox, VMware, Hyper-V, UTM, or another locally approved hypervisor:

| VM | Function | Virtual network | Address |
|---|---|---|---|
| Kali Linux | Authorised security tester | `MSS-LAB` | `192.168.56.10/24` |
| Windows Server | Real version of `INTRANET-SRV` | `MSS-LAB` | `192.168.56.20/24` |

Configure both VM adapters as **Internal Network** or **Host-only Network** and give the network the exact same name, such as `MSS-LAB`.

Do not use Bridged networking. Bridged mode could expose scans to the home, university, or workplace network.

Create deliberately safe test content only. Do not copy real employee information, real credentials, or production data into the lab.

### 8.3 Configure The Kali Address

Start Kali and identify its interface and connection name:

```bash
ip -brief address
nmcli connection show
```

Configure the `MSS-LAB` connection. Replace `Wired connection 1` only if Kali displays a different connection name:

```bash
sudo nmcli connection modify "Wired connection 1" \
  ipv4.method manual \
  ipv4.addresses 192.168.56.10/24 \
  ipv4.gateway "" \
  ipv4.dns ""

sudo nmcli connection up "Wired connection 1"
```

Verify:

```bash
ip -brief address
ip route
```

The route table should show the isolated `192.168.56.0/24` network. It should not route test traffic to university or unrelated networks.

### 8.4 Configure The Windows Server Address

On Windows Server, open:

```text
Control Panel
> Network and Internet
> Network Connections
> Ethernet Properties
> Internet Protocol Version 4 (TCP/IPv4)
> Properties
```

Enter:

```text
IP address:      192.168.56.20
Subnet mask:     255.255.255.0
Default gateway: leave empty
DNS server:      leave empty for the first test
```

Alternatively, use an Administrator PowerShell window after confirming the adapter name:

```powershell
Get-NetAdapter
New-NetIPAddress -InterfaceAlias "Ethernet" -IPAddress 192.168.56.20 -PrefixLength 24
```

Verify:

```powershell
Get-NetIPAddress -AddressFamily IPv4
Get-NetRoute -AddressFamily IPv4
```

### 8.5 Test Basic Connectivity

From Kali:

```bash
ping -c 4 192.168.56.20
```

Windows Firewall may block ping even when the network is working. Do not disable the firewall. If ping fails, continue after IIS installation and test TCP/80 instead.

If neither ping nor TCP/80 works, verify:

1. Both VMs are running.
2. Both adapters use the same `MSS-LAB` internal/host-only network.
3. Kali uses `192.168.56.10/24`.
4. Windows Server uses `192.168.56.20/24`.
5. Neither adapter is using Bridged mode.

### 8.6 Install IIS To Create The Real Intranet Target

Open PowerShell as Administrator on Windows Server:

```powershell
Install-WindowsFeature Web-Server -IncludeManagementTools
Get-Service W3SVC
Start-Service W3SVC
```

Create an MSS Ltd test page:

```powershell
Set-Content -Path "C:\inetpub\wwwroot\index.html" -Value @"
<!DOCTYPE html>
<html>
<head><title>MSS Ltd Test Intranet</title></head>
<body>
<h1>MSS Ltd Test Intranet</h1>
<p>Authorised vulnerability-assessment laboratory.</p>
</body>
</html>
"@
```

Create a narrowly scoped inbound firewall rule if IIS installation did not create an enabled HTTP rule:

```powershell
New-NetFirewallRule `
  -DisplayName "MSS Lab IIS HTTP" `
  -Direction Inbound `
  -Protocol TCP `
  -LocalPort 80 `
  -RemoteAddress 192.168.56.0/24 `
  -Action Allow
```

Do not disable Windows Defender Firewall. Permit only the service and source network required by the test.

Verify the listening service:

```powershell
Get-NetTCPConnection -State Listen |
  Sort-Object LocalPort |
  Select-Object LocalAddress, LocalPort, OwningProcess
```

From Kali, open:

```text
http://192.168.56.20
```

Or test with:

```bash
curl -I http://192.168.56.20
```

Expected result: an HTTP response from Microsoft IIS and the MSS Ltd test page in a browser.

### 8.7 Take Clean Snapshots

Before scanning, shut down or safely pause the VMs and create:

```text
Kali-before-MSS-testing
Windows-INTRANET-clean-IIS
```

Record the Windows Server version, installed IIS role, addresses, enabled services, and snapshot names in the evidence log.

### 8.8 Run The First Kali Baseline

From Kali:

```bash
nmap -sn 192.168.56.0/24
```

Windows may ignore ping discovery. Scan the known authorised target with `-Pn`:

```bash
nmap -Pn -sT -sV --version-light -T3 \
  -p 80,135,139,443,445,3389 \
  192.168.56.20 -oA mss-windows-baseline
```

Interpret the result against the installed roles. TCP/80 is expected for the initial IIS test. Other open ports must be explained, restricted, or disabled if they are unnecessary.

Run the web checks:

```bash
nikto -h http://192.168.56.20 \
  -Format txt -output mss-windows-nikto.txt

curl -I http://192.168.56.20
```

Validate every Nikto observation manually before calling it a confirmed finding.

### 8.9 Reproduce The MSS Ltd Subnets Later

The flat `192.168.56.0/24` lab proves that Kali can test the representative Windows Server. To reproduce segmentation, add a virtual router/firewall and create separate internal networks:

| Virtual network | Subnet | Example system |
|---|---|---|
| `MSS-MARKETING` | `192.168.10.0/27` | Kali acting as an ordinary user at `192.168.10.30` |
| `MSS-SERVERS` | `192.168.40.0/27` | Windows `INTRANET-SRV` at `192.168.40.12` |
| `MSS-MANAGEMENT` | `192.168.50.0/28` | Kali acting as an administrator at `192.168.50.14` |
| `MSS-BACKUP` | `192.168.60.0/28` | Optional backup/archive VM at `192.168.60.11` |

Give the virtual router `.1` in each subnet. Configure rules equivalent to the Packet Tracer policy:

| Source | Destination/service | Expected result |
|---|---|---|
| Marketing | Intranet HTTP/HTTPS | Allow |
| Marketing | Server management/SSH/RDP | Deny |
| Marketing | Backup network | Deny |
| Management | Approved server management | Allow |
| Management | Backup network | Allow |

Move Kali's adapter between `MSS-MARKETING` and `MSS-MANAGEMENT` to test the ordinary-user and authorised-administrator perspectives. Do not attach it to both networks simultaneously unless the test plan requires it; otherwise Kali could accidentally become an alternate path between security zones.

### 8.10 How To Describe This In The Report

Use wording similar to:

> Cisco Packet Tracer was used to validate the MSS Ltd topology, VLANs, ACLs, management restrictions, and redundancy. Because Packet Tracer devices are internal simulations and cannot be scanned directly by an external Kali VM, a Windows Server VM running IIS was created as a representative implementation of `INTRANET-SRV`. Kali assessed this authorised VM on an isolated virtual network. Findings were mapped to the equivalent MSS Ltd server role and were not presented as direct scans of the Packet Tracer device.

### 8.11 Command Placeholders Used Below

In the remaining phases, the initial Windows Server lab uses:

```text
<KALI_IP>       192.168.56.10
<TARGET_IP>     192.168.56.20
<LAB_SUBNET>    192.168.56.0/24
<WEB_URL>       http://192.168.56.20
```

Replace these only when the authorised lab uses different addresses. Never substitute a public, university, workplace, or unrelated home-network address.

---

## 9. Phase 3 - Information Gathering With Nmap

Nmap addresses the information-gathering requirement by identifying live hosts, exposed TCP/UDP ports, and service versions.

### 9.1 Confirm Nmap and create a baseline

```bash
nmap --version
nmap -sn <LAB_SUBNET>
```

Save normal output:

```bash
nmap -sn <LAB_SUBNET> -oA mss-host-discovery
```

Record:

- Expected hosts found.
- Unexpected hosts found.
- Expected hosts missing.
- Kali source address and scan time.

### 9.2 Perform a safe TCP service scan

Run against one authorised server:

```bash
nmap -sT -sV --version-light -T3 --top-ports 100 <TARGET_IP> -oA mss-server-services
```

Use `-sT` because it is clear and suitable for a basic authorised lab demonstration. Do not begin with every port, every script, or aggressive timing.

Compare every open port with the asset register:

| Observation | Interpretation |
|---|---|
| Expected web port open | Confirm it is required and patched |
| Telnet port 23 open | High concern; replace with SSH |
| FTP port 21 open | Plain-text protocol; restrict or replace with SFTP/FTPS |
| Unplanned database/admin port open | Reduce exposure and investigate ownership |
| Version banner returned | Use version only as a lead; verify before assigning a CVE |

### 9.3 Check selected UDP services

UDP scans are slower and can produce ambiguous `open|filtered` results. Test only the services relevant to the server role:

```bash
sudo nmap -sU -T2 -p 53,67,68,69,123,161 <TARGET_IP> -oA mss-selected-udp
```

Do not report `open|filtered` as definitely open. Explain the uncertainty and corroborate with server configuration or an authenticated scan.

### 9.4 Safe Nmap scripts

Use non-destructive default and banner scripts only after the basic scan:

```bash
nmap -sT -sV -T3 --script default,banner -p <OPEN_PORTS> <TARGET_IP> -oA mss-safe-scripts
```

Review script documentation before use. Do not use denial-of-service, brute-force, or exploitation scripts.

### 9.5 Nmap evidence and finding example

Capture:

- Command and target.
- Host status.
- Open ports and identified services.
- Asset-register comparison.
- One remediation decision.
- Retest result after the port/service is removed or restricted.

Example finding text:

> Nmap identified TCP/21 on the archive test server although only secure administrative transfer was intended. FTP provides no transport encryption and increases credential-disclosure risk. Replace it with SFTP, restrict the source network, and verify closure of TCP/21 by rescanning.

---

## 10. Phase 4 - Packet Analysis With Wireshark Or TShark

Wireshark addresses sniffing/traffic-analysis requirements. Capture only traffic generated by the group in the isolated lab.

### 10.1 Select the interface

On Kali:

```bash
ip -brief address
sudo tshark -D
```

Identify the internal-lab interface. Do not capture on an unrelated Wi-Fi or campus interface.

### 10.2 Capture a controlled web transaction

Start a capture:

```bash
sudo tshark -i <LAB_INTERFACE> -f "host <TARGET_IP>" -w mss-web-test.pcapng
```

Generate one lab HTTP request, stop the capture with `Ctrl+C`, then summarise:

```bash
tshark -r mss-web-test.pcapng -Y "http || dns || tcp" -q -z conv,tcp
```

Useful Wireshark display filters:

```text
ip.addr == <TARGET_IP>
http
dns
ftp
smtp
pop
arp
tcp.flags.syn == 1 && tcp.flags.ack == 0
```

### 10.3 Demonstrate encryption improvement

Perform one HTTP request to a test page containing no sensitive data. Then enable HTTPS on the lab server and repeat.

Compare:

| Test | Expected observation |
|---|---|
| HTTP | Application requests/content may be readable in capture |
| HTTPS | TLS records are visible but application content is encrypted |

Never place real passwords in the demonstration. The evidence should prove protocol exposure, not disclose credentials.

### 10.4 ARP and DHCP observations

Use these filters during normal traffic:

```text
arp
bootp
```

Record the legitimate default-gateway MAC and authorised DHCP server. Discuss how unexpected ARP replies or an unapproved DHCP server could redirect traffic.

If the group demonstrates spoofing, do so only on an isolated two-VM network with disposable accounts, explicit tutor approval, and no route outside the lab. A packet-level discussion and defensive validation are sufficient for the coursework; successful interception is not required.

### 10.5 Evidence

Include a screenshot with:

- Capture interface.
- Filter.
- Source and destination.
- Relevant protocol fields.
- No sensitive content.

State the limitation that a switched network normally restricts a host to its own and broadcast traffic unless the observer is on-path, uses a mirror port, or successfully manipulates traffic.

---

## 11. Phase 5 - Vulnerability Scanning With Greenbone/OpenVAS Or Nessus

Use one scanner. Greenbone Community Edition is suitable on Kali; Nessus Essentials is a reasonable Windows/Linux alternative if already available.

### 11.1 Scanner setup principles

1. Update feeds before the scheduled lab session.
2. Add only explicit in-scope lab IP addresses.
3. Prefer authenticated scanning using a dedicated, low-privilege audit account.
4. Select a safe/basic scan profile.
5. Disable denial-of-service checks.
6. Scan one server first.
7. Monitor CPU, memory, logs, and service availability.

For Greenbone on Kali, the typical local workflow is:

```bash
sudo gvm-check-setup
sudo gvm-start
```

Open the local Greenbone web interface shown by the command, create a target containing only `<TARGET_IP>`, then create a task using a safe full-and-fast profile. Interface wording varies by version, so record the installed version and chosen profile.

### 11.2 Validate scanner findings

For every high or critical scanner result:

1. Confirm the target address and service.
2. Confirm the detected version using package/configuration evidence.
3. Read the finding description and prerequisites.
4. Reject false positives with a written reason.
5. Do not exploit a finding merely because the scanner suggests it.
6. Map the confirmed issue to an MSS Ltd business impact.

Use a table:

| Scanner item | Scanner severity | Validation | MSS impact | Final rating | Action |
|---|---|---|---|---|---|
| | | Confirmed/false positive/unconfirmed | | | |

### 11.3 Authenticated versus unauthenticated scan

If time permits, compare both:

- Unauthenticated scan: shows the attacker's external view of exposed services.
- Authenticated scan: can identify local packages, missing updates, and configuration weaknesses more accurately.

Do not place scan credentials in a screenshot or exported report.

### 11.4 Remediate and retest

Apply one safe correction, such as:

- Remove an unnecessary service.
- Install an approved security update.
- Restrict a port to the management subnet.
- Correct TLS or web-server configuration.

Repeat the same scan and record whether the finding is absent, reduced, or unchanged.

---

## 12. Phase 6 - Web Analysis With Nikto And Manual Checks

The intranet and three database portals make web analysis directly relevant. Test the isolated web target, not a public site and not the university network.

### 12.1 Nikto baseline

```bash
nikto -Version
nikto -h <WEB_URL> -Format txt -output mss-nikto-baseline.txt
```

If HTTPS is enabled on the lab server:

```bash
nikto -h https://<TARGET_IP> -ssl -Format txt -output mss-nikto-https.txt
```

Nikto commonly reports missing headers, server banners, default files, and risky methods. Treat each item as a lead and manually verify it.

### 12.2 Low-impact manual checks

```bash
curl -I <WEB_URL>
curl -s -o /dev/null -w "%{http_code}\n" <WEB_URL>
```

Review:

- Is HTTP redirected to HTTPS?
- Is a detailed server version exposed?
- Are authentication and authorisation required for a database portal?
- Are directory listings or default pages present?
- Are security headers appropriate for the real application?
- Are error pages revealing paths, code, or platform details?

Packet Tracer portals do not implement real web authentication or databases. Report this as a simulation limitation and explain the production control: HTTPS, application login, role-based access, secure session handling, input validation, database least privilege, logging, and patching.

### 12.3 Do not claim unsupported web findings

Do not invent SQL injection, cross-site scripting, or remote-code-execution findings against Packet Tracer's static pages. Such a finding requires a real, deliberately vulnerable lab application and reproducible evidence.

---

## 13. Phase 7 - Password And Authentication Audit

The preferred assessment is a policy/configuration review plus a small rate-limited test against an intentionally created lab account.

### 13.1 Review checklist

Check whether:

- Every placeholder password was replaced.
- Each administrator has an individual account.
- Privileged access uses a strong secret and SSH.
- Shared departmental or administrator credentials are prohibited.
- Account lockout/rate limiting exists on real services.
- MFA is used for VPN, e-mail, and privileged access in production.
- Leavers and dormant accounts are disabled promptly.
- Passwords are stored using modern salted hashes by applications.

`service password-encryption` on Cisco devices is not a substitute for strong secrets or central AAA. Explain that it mainly prevents casual reading of some configuration passwords.

### 13.2 Controlled online password test

Only if explicitly authorised, create a disposable account such as `audit-test` on an isolated lab service. Use a wordlist containing a few group-created test values, not leaked real passwords.

Example for a lab SSH service:

```bash
hydra -l audit-test -P mss-test-passwords.txt -t 1 -W 3 -f ssh://<TARGET_IP> -o mss-hydra-audit.txt
```

Safety controls in this command:

- One disposable username.
- One task at a time.
- Delay/wait behaviour.
- Stop after the first valid result.
- One authorised target.

Stop after proving the control point. Do not test employee accounts. Record whether rate limiting, lockout, monitoring, and alerting occurred.

### 13.3 Safer offline demonstration

For an offline password-strength example, generate test hashes from invented passwords only and use an approved offline auditing tool. Do not extract or crack real hashes. The learning outcome is to compare weak versus strong test passwords and then recommend length, uniqueness, MFA, and rate limiting.

---

## 14. Phase 8 - Controlled Validation With Metasploit

Metasploit is optional and should be used only to validate one known weakness in a disposable, deliberately vulnerable VM. A scanner result and remediation/retest may already provide sufficient evidence.

### 14.1 Decision gate

Proceed only when all are true:

- Written approval explicitly permits exploitation.
- Target is a disposable lab VM.
- Target version and vulnerability are confirmed.
- VM snapshot exists.
- No route leaves the isolated lab.
- Payload creates no persistence and accesses no real data.
- A stop/recovery plan is recorded.

### 14.2 Safe workflow

Start Metasploit and search by the confirmed identifier:

```bash
msfconsole
search cve:<CONFIRMED_CVE>
info <MODULE>
```

Read `info` fully. Confirm supported versions, required options, side effects, and references. Use a module check where supported:

```text
use <APPROVED_MODULE>
set RHOSTS <TARGET_IP>
check
```

Do not select a payload or run the module unless the written test plan explicitly allows it. A successful safe `check`, followed by patching and a failed recheck, is a strong implementation example.

### 14.3 Evidence and cleanup

Record:

- Confirmed software/version.
- CVE and why it applies.
- Module information.
- Check result.
- Remediation.
- Retest.
- Snapshot restoration.

Never include persistence, privilege escalation, lateral movement, data extraction, or destructive actions in this coursework demonstration.

---

## 15. Phase 9 - Network-Control Validation

### 15.1 Management ACL test matrix

| Source | Target | Test | Expected |
|---|---|---|---|
| VLAN 50 Admin | Core/access management IP | SSH | Allow |
| VLAN 10 user | Core/access management IP | SSH | Deny |
| VLAN 20 user | Core/access management IP | SSH | Deny |
| VLAN 30 user | Core/access management IP | SSH | Deny |

### 15.2 Backup ACL test matrix

| Source | Destination | Expected |
|---|---|---|
| VLAN 50 Admin | VLAN 60 backup/archive | Allow |
| VLAN 40 production server | VLAN 60 backup/archive | Allow |
| VLAN 10 Marketing | VLAN 60 backup/archive | Deny |
| VLAN 20 Sales | VLAN 60 backup/archive | Deny |
| VLAN 30 Support | VLAN 60 backup/archive | Deny |

### 15.3 Required business-access matrix

| Source | Destination | Expected |
|---|---|---|
| Every user VLAN | Intranet | Allow |
| Every user VLAN | Mail | Allow required mail services |
| Every user VLAN | All three department DB portals | Allow, then authenticate at application layer in production |
| Every user VLAN | Internet via NAT | Allow according to policy |

### 15.4 Rule-review method

For every ACL:

1. State the business requirement.
2. Identify source, destination, protocol, and port.
3. Check rule order and implicit deny behaviour.
4. Check both redundant forwarding paths.
5. Test an allowed case.
6. Test a denied case.
7. inspect counters.
8. Save the configuration.

A control is not proven merely because the rule exists in a configuration.

---

## 16. Defence Implementation Mapped To Findings

### 16.1 Immediate Packet Tracer controls

Confirm the existing design has:

- SSH version 2 only on routers and switches.
- Local secret-based administrative account.
- VTY access class permitting only VLAN 50.
- Backup ACL allowing VLANs 40 and 50 and denying user VLANs.
- VLAN 99 as an unused native/black-hole VLAN.
- Explicit trunk allowed lists.
- User access ports fixed to access mode.
- Port security with sticky MAC and violation restriction.
- BPDU Guard and PortFast on access ports.
- Unused ports assigned to VLAN 99 and shut down.
- NAT overload at `EDGE-R1`.
- HSRP, Rapid PVST+, dual uplinks, and tested failover.

After every correction:

```cisco
end
write memory
show running-config
```

Then repeat the exact failed test and record the new result.

### 16.2 Production controls not realistically simulated in Packet Tracer

| Finding area | Recommended production defence |
|---|---|
| Weak/shared device credentials | Central TACACS+/RADIUS, individual accounts, MFA for privileged access, password vault, removal of dormant accounts |
| Plain-text web/mail/file transfer | HTTPS/TLS, secure mail submission/retrieval, SFTP, valid certificates, disable legacy protocols |
| Web/database exposure | Application authentication, RBAC, database roles, reverse proxy/WAF where justified, input validation, secrets management |
| Rogue devices and Layer 2 attacks | 802.1X/NAC, DHCP snooping, Dynamic ARP Inspection, IP Source Guard, port security |
| Unpatched services | Asset owner, patch SLA, tested maintenance window, authenticated scanning, supported operating systems |
| Phishing/malware | Secure e-mail gateway, endpoint detection and response, least privilege, application control, awareness exercises |
| Poor visibility | Central syslog/SIEM, NTP, IDS/IPS, NetFlow/telemetry, alert ownership and retention policy |
| Backup risk | 3-2-1 strategy, offline/immutable copy, encrypted backups, separate credentials, scheduled restore tests |
| Edge single point of failure | Two edge firewalls/routers, diverse ISP links, tested failover, spare hardware/configuration |
| Server failure | Service redundancy, configuration backups, clustering/replication as justified, UPS and monitoring |
| Physical threats | Locked racks, controlled server-room access, CCTV where appropriate, UPS, fire suppression, environmental sensors |

### 16.3 Additional Cisco controls for a production-capable platform

Where supported, propose:

```cisco
! Example concept only - syntax/support must be verified on the selected platform
ip dhcp snooping
ip dhcp snooping vlan 10,20,30,50
ip arp inspection vlan 10,20,30,50
```

Trust only the legitimate uplinks/DHCP path. Do not blindly paste these commands into the coursework topology; Packet Tracer model support varies, and incorrect trust settings can block DHCP.

### 16.4 Defence-to-finding traceability

Use this report table:

| Finding ID | Confirmed weakness | Evidence | Selected control | Owner | Target date | Retest evidence | Residual risk |
|---|---|---|---|---|---|---|---|
| F-03 | HTTP exposed readable test content | EV-014 | Enable HTTPS and redirect HTTP | Server admin | | EV-021 | Low/Medium |

Every major finding should have a defence, owner, priority, and retest.

---

## 17. Retesting Procedure

Retesting is required to show that the defence changes the result.

1. Preserve the original evidence.
2. Record the configuration or package change.
3. Use the same source, target, tool, options, and test data.
4. Repeat the test.
5. Compare before and after.
6. Check that required business services still work.
7. Rate residual risk.
8. Save configuration and a new VM snapshot.

Example:

| Stage | Result |
|---|---|
| Before | Nmap shows TCP/21 FTP open on archive lab server |
| Change | FTP disabled; SFTP restricted to management source |
| Security retest | TCP/21 closed; approved secure service reachable only from admin network |
| Regression test | Authorised archive transfer still works |
| Residual risk | Low, subject to patching and account monitoring |

---

## 18. Regular Security Maintenance Guideline

| Frequency | Activity | Evidence/owner |
|---|---|---|
| Continuous/daily | Monitor critical availability, IDS/EDR/SIEM alerts, failed privileged logins, and backup jobs | Dashboard/ticket; operations |
| Daily | Confirm DHCP, DNS, mail, intranet, database, and Internet-edge health | Service check; operations |
| Weekly | Review central logs, admin changes, new devices, port-security violations, and unusual traffic | Signed review record; security/network team |
| Weekly | Verify backup completion and protect backup credentials | Backup report; backup owner |
| Monthly | Run authenticated vulnerability scans on servers and network devices | Scan report and remediation tickets |
| Monthly | Review accounts, privileged membership, dormant users, ACL/firewall changes, and unused ports | Access review; system owners |
| Monthly | Apply approved OS, application, firmware, and endpoint patches in a tested window | Change record; asset owners |
| Quarterly | Restore a sample backup and document recovery time and integrity | Restore report; service owner |
| Quarterly | Test HSRP, STP/uplink, edge, and critical-service failover | Resilience test record |
| Quarterly | Conduct a scoped penetration test and safe phishing-awareness exercise | Approved test report; security lead |
| Six-monthly | Review VLAN design, firewall/ACL rules, certificates, service exposure, and capacity | Architecture review |
| Annually | Refresh risk assessment, incident plan, disaster recovery plan, supplier/warranty review, and staff training | Management approval |
| After a change | Back up configuration, run security and regression tests, update diagrams and asset register | Change ticket |
| After an incident | Preserve evidence, contain, eradicate, recover, reset affected credentials, and complete lessons learned | Incident report |

Use patch priorities:

| Priority | Example | Target treatment |
|---|---|---|
| Emergency | Actively exploited critical issue on exposed service | Immediate isolation and emergency change |
| High | Confirmed high-risk issue with plausible MSS impact | Earliest approved maintenance window |
| Medium | Requires internal access or has compensating controls | Planned monthly cycle |
| Low | Hardening improvement with limited impact | Track and include in routine work |

Exact service-level targets should be approved by MSS Ltd management rather than invented by the testing group.

---

## 19. Incident Response Link

If a test or monitoring alert indicates a real compromise:

1. Stop testing and note the time.
2. Inform the authorised contact.
3. Isolate the affected host without destroying evidence.
4. Preserve logs, packet captures, and volatile information according to policy.
5. Identify affected accounts, systems, and data.
6. Remove the cause and patch the vulnerability.
7. Reset exposed credentials and revoke sessions/keys.
8. Restore from verified clean backups if required.
9. Monitor closely after recovery.
10. Record lessons learned and update controls, diagrams, and the risk register.

Do not attempt unapproved counterattacks or conceal an accidental outage.

---

## 20. Evidence Capture Checklist

- [ ] Signed/recorded scope and rules of engagement.
- [ ] Asset register and network diagram.
- [ ] Clean Packet Tracer and VM snapshots.
- [ ] `show running-config` review evidence.
- [ ] VLAN, trunk, STP, HSRP, route, and interface state.
- [ ] Management SSH allowed from VLAN 50.
- [ ] Management SSH blocked from a user VLAN.
- [ ] Backup access allowed from VLAN 50 and VLAN 40.
- [ ] Backup access blocked from VLANs 10, 20, and 30.
- [ ] ACL placement and counters on both cores.
- [ ] Unused ports in VLAN 99 and shut down.
- [ ] Port-security test and counter.
- [ ] Packet Tracer plain-text protocol observation.
- [ ] Kali lab isolation and route table.
- [ ] Nmap host discovery and service scan.
- [ ] Wireshark/TShark controlled capture.
- [ ] Greenbone/OpenVAS or Nessus report with validated findings.
- [ ] Nikto result and manual web-header verification.
- [ ] Password-policy review and, if authorised, disposable-account test.
- [ ] Optional Metasploit safe check and approval record.
- [ ] Before/after remediation evidence.
- [ ] Regression test showing business service still works.
- [ ] Final risk register with residual risk.
- [ ] Group activity list for the appendix.

Each figure caption must answer:

1. What was tested?
2. What was expected?
3. What actually happened?
4. What vulnerability or control does it demonstrate?
5. Why does it matter to MSS Ltd?

---

## 21. Recommended Report Structure For The 30% Component

### 21.1 Scope and method

Briefly state:

- Authorised lab scope.
- Packet Tracer and isolated VM limitations.
- Test phases.
- Risk method.
- Safety controls.

### 21.2 Security vulnerabilities - 20%

Use the final risk register, not a generic list. Focus on MSS Ltd assets, evidence, likelihood, impact, and business effect. Distinguish confirmed findings, design limitations, and untested hypotheses.

### 21.3 Tools and implementation examples - 40%

For each selected tool include:

- Why it is relevant to this network.
- Exact authorised target and command/profile.
- One concise evidence figure.
- Interpretation of the result.
- Limitation or false-positive consideration.

A strong set is:

1. Cisco/Packet Tracer configuration and path testing.
2. Nmap discovery/service identification.
3. Wireshark/TShark protocol analysis.
4. Greenbone/OpenVAS or Nessus scanning.
5. Nikto plus manual HTTP header review.
6. A controlled authentication audit.
7. Optional Metasploit `check` when explicitly approved.

### 21.4 Defence mechanisms - 30%

Map every confirmed high/critical finding to a specific control. Separate controls implemented in Packet Tracer from production recommendations. Include remediation and retest evidence.

### 21.5 Maintenance guideline - 10%

Include the maintenance table, ownership, change-triggered checks, backup restore tests, patching, rescanning, and incident review.

### 21.6 Appendix

Place here:

- Full commands and long outputs.
- Extra screenshots.
- Scanner export extracts.
- Authorisation/scope sheet.
- Detailed risk register.
- Group activity list showing each member's contribution.

---

## 22. Report-Ready Tool Summary

| Tool/technique | MSS Ltd purpose | Implementation example | Main limitation |
|---|---|---|---|
| Cisco show commands | Audit actual segmentation and device hardening | Inspect trunks, ACLs, VTY, ports, HSRP, STP, and routes | Configuration presence does not prove enforcement |
| Packet Tracer path tests | Prove allowed and blocked flows | User/admin tests to management and backup VLANs | Simulated protocols and hosts |
| Nmap | Discover hosts, ports, and services | Low-rate scan of authorised lab server | Version inference can be wrong |
| Wireshark/TShark | Examine approved traffic | Compare lab HTTP and HTTPS capture | Observer must be able to see the traffic |
| Greenbone/OpenVAS or Nessus | Identify patch/configuration issues | Safe authenticated scan of one lab server | False positives require validation |
| Nikto/curl | Review web exposure and headers | Scan and manually verify isolated intranet | Does not prove complex application vulnerabilities |
| Hydra | Test rate limiting and one disposable account | One user, tiny test list, one target, low concurrency | Online tests can lock accounts or create load |
| Metasploit | Validate one confirmed lab weakness | Module information and safe `check` | Optional; exploitation increases risk |

Wireless testing is not relevant to the current wired topology because no wireless access point, WLAN, or wireless client is present. State this clearly. If wireless is proposed as a future enhancement, assess it only after an authorised WLAN is designed and placed in scope.

---

## 23. Group Activity List Template

The coursework brief requires an activity list in the appendix.

| Member | Student ID | Agreed responsibility | Work completed | Evidence IDs/files | Review performed by |
|---|---|---|---|---|---|
| | | Scope and asset register | | | |
| | | Packet Tracer security tests | | | |
| | | Nmap and packet analysis | | | |
| | | Vulnerability/web scanning | | | |
| | | Defences, retest, and maintenance plan | | | |

All members should review the final findings and understand the commands attributed to the group.

---

## 24. Final Completion Checklist

- [ ] Testing was performed only in an authorised isolated lab.
- [ ] Packet Tracer limitations are stated accurately.
- [ ] Every reported finding has evidence or is labelled as a hypothesis/design limitation.
- [ ] Findings are specific to MSS Ltd and not generic textbook definitions.
- [ ] Likelihood and impact ratings are justified.
- [ ] Tool examples include commands/profiles, target, output, and interpretation.
- [ ] Scanner findings were manually validated.
- [ ] No unsupported vulnerabilities were invented.
- [ ] Every major finding maps to a defence.
- [ ] At least one defence has before-and-after retest evidence.
- [ ] Required business access still works after remediation.
- [ ] Residual risks and single points of failure are acknowledged.
- [ ] The regular maintenance plan includes owners and evidence.
- [ ] Passwords and sensitive packet contents are redacted.
- [ ] Full outputs and the group activity list are in the appendix.
- [ ] The complete coursework report remains within 20 pages.
- [ ] The final work has been proofread by all group members.

---

## 25. Suggested Test Schedule

| Session | Work |
|---:|---|
| 1 | Agree authorisation, scope, asset register, risk method, and evidence names |
| 2 | Audit Packet Tracer configurations and run access-control tests |
| 3 | Build and isolate Kali/target VMs; take snapshots |
| 4 | Run Nmap discovery/service scans and Wireshark/TShark captures |
| 5 | Run safe vulnerability and web scans; validate results |
| 6 | Perform approved authentication or optional safe validation test |
| 7 | Apply selected defences and repeat security/regression tests |
| 8 | Finalise risk register, maintenance plan, captions, appendix, and activity list |

Stop at the end of each session, back up evidence, and record what remains to be tested.
