# MSS Ltd Full Implementation Steps

This file gives a complete start-to-end implementation order for the MSS Ltd Cisco Packet Tracer network. It follows the intended top-to-bottom flow:

1. Internet and ISP
2. Edge router
3. Stateful perimeter firewall
4. Redundant core switches
5. Access switches
6. Server farm
7. Department PCs
8. Security controls
9. Functional testing
10. Redundancy testing
11. Evidence and final checks

The design supports the coursework requirements for Marketing, Sales, General Support, intranet, e-mail, departmental database access, backup services, Internet access, redundancy, security and future growth.

---

## 1. Final Device List

### 1.1 Routers

| Device | Packet Tracer model | Purpose |
|---|---|---|
| ISP-R1 | 2911 router | Simulates the external ISP network |
| EDGE-R1 | 2911 router | Company edge router, NAT and default Internet route |

### 1.2 Firewall

| Device | Packet Tracer model | Purpose |
|---|---|---|
| FW-ASA1 | ASA 5505 | Stateful perimeter inspection and outside access policy |

### 1.3 Core switches

| Device | Packet Tracer model | Purpose |
|---|---|---|
| CORE-SW1 | 3560-24PS multilayer switch | Main core switch, inter-VLAN routing, HSRP and STP |
| CORE-SW2 | 3560-24PS multilayer switch | Redundant core switch, HSRP and STP backup/load sharing |

### 1.4 Access switches

| Device | Packet Tracer model | Purpose |
|---|---|---|
| SW-MKT | 2960-24TT | Floor 1 Marketing users |
| SW-SALES-A | 2960-24TT | Floor 2 Sales users 01-24 |
| SW-SALES-B | 2960-24TT | Floor 2 Sales user 25 and future growth |
| SW-SUPPORT | 2960-24TT | Floor 3 General Support users |
| SW-SERVER | 2960-24TT | Server farm, backup VLAN and Admin PC |

### 1.5 End devices

| Device group | Quantity | Names |
|---|---:|---|
| Marketing PCs | 20 | MKT-PC01 to MKT-PC20 |
| Sales PCs | 25 | SALES-PC01 to SALES-PC25 |
| Support PCs | 15 | SUP-PC01 to SUP-PC15 |
| Admin PC | 1 | ADMIN-PC |

### 1.6 Servers

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
| CORE-SW1 to FW-ASA1 inside | CORE-SW1 Fa0/24: 10.255.0.2/29 | FW-ASA1 VLAN 1: 10.255.0.1/29 |
| CORE-SW2 to FW-ASA1 inside | CORE-SW2 Fa0/24: 10.255.0.3/29 | FW-ASA1 VLAN 1: 10.255.0.1/29 |
| FW-ASA1 outside to EDGE-R1 | FW-ASA1 VLAN 2: 10.255.1.2/30 | EDGE-R1 G0/0: 10.255.1.1/30 |
| EDGE-R1 to ISP-R1 | EDGE-R1 G0/2: 203.0.113.2/30 | ISP-R1 G0/0: 203.0.113.1/30 |
| ISP-R1 to Internet server | ISP-R1 G0/1: 198.51.100.1/24 | INTERNET-SRV Fa0: 198.51.100.10/24 |

The firewall-to-core transit uses HSRP virtual address `10.255.0.4/29`; `FW-ASA1` uses it as the resilient next hop for all internal subnets.

---

## 3. Build The Topology From Top To Bottom - Literal Click-By-Click Guide

Complete this section in order. Do not configure IP addresses yet. First place, rename and connect every device so the physical topology is complete.

Packet Tracer has two selection rows at the bottom-left:

1. The first row contains broad categories such as **Network Devices** and **End Devices**.
2. The second row contains subcategories such as **Routers** and **Switches**.
3. The panel to the right shows the device models that can be dragged to the workspace.
4. The lightning-bolt icon is **Connections**. Use **Automatically Choose Connection Type** for the instructions below. Packet Tracer will choose the suitable copper cable.

### Step 3.1 - Create and save the empty project

1. Open Cisco Packet Tracer.
2. Click **File > New**.
3. If Packet Tracer asks whether to save another file, make the appropriate choice.
4. Click **File > Save As**.
5. Enter `MSS_Ltd_Final.pkt` as the filename.
6. Choose the coursework folder.
7. Click **Save**.
8. Make sure **Logical** is selected at the top-left of the workspace.
9. Keep the workspace empty and continue to Step 3.2.

### Step 3.2 - Place and rename the external Internet server

This will be the first and highest device on the page.

1. At the bottom-left, click **End Devices**.
2. In the device list, find **Server**.
3. Click the **Server** icon once.
4. Move the pointer to the top-centre of the white workspace.
5. Click once to place the server.
6. Press `Esc` so Packet Tracer stops placing more servers.
7. Click the newly placed server.
8. Open its **Config** tab.
9. In **Display Name**, delete the default name such as `Server0`.
10. Enter `INTERNET-SRV`.
11. Close the device window.
12. Confirm that `INTERNET-SRV` appears under the server icon.

### Step 3.3 - Place and rename the ISP router

1. At the bottom-left, click **Network Devices**.
2. Click the **Routers** subcategory.
3. Find and click the **2911** router.
4. Move the pointer directly below `INTERNET-SRV`.
5. Leave enough vertical space for a cable and click once to place it.
6. Press `Esc`.
7. Click the new router.
8. Open **Config**.
9. Change **Display Name** from `Router0` to `ISP-R1`.
10. Close the device window.

### Step 3.4 - Connect INTERNET-SRV to ISP-R1

1. Click the **Connections** lightning-bolt icon at the bottom-left.
2. Click **Automatically Choose Connection Type**. It is the lightning-bolt connection option.
3. Click `INTERNET-SRV` on the workspace.
4. When the port list appears, click `FastEthernet0`.
5. Click `ISP-R1`.
6. When the port list appears, click `GigabitEthernet0/1`.
7. Wait a few seconds.
8. A cable should now run vertically from `INTERNET-SRV Fa0` to `ISP-R1 G0/1`.
9. The link may be red until the router interface is configured and enabled later. This is normal.
10. Press `Esc`.

### Step 3.5 - Place and rename the company edge router

1. At the bottom-left, click **Network Devices**.
2. Click **Routers**.
3. Click the **2911** router.
4. Place this router directly below `ISP-R1`.
5. Press `Esc`.
6. Click the new router and open **Config**.
7. Change **Display Name** to `EDGE-R1`.
8. Close the device window.

### Step 3.6 - Connect ISP-R1 to EDGE-R1

1. Click **Connections**.
2. Click **Automatically Choose Connection Type**.
3. Click `ISP-R1`.
4. Select `GigabitEthernet0/0`.
5. Click `EDGE-R1`.
6. Select `GigabitEthernet0/2`.
7. Confirm that the cable appears between the two routers.
8. Press `Esc`.

The top of the topology should now read from top to bottom:

```text
INTERNET-SRV
     |
   ISP-R1
     |
  EDGE-R1
```

### Step 3.6A - Place and rename the firewall

1. Click **Network Devices > Security**.
2. Select the **ASA 5505** and place it directly below `EDGE-R1`.
3. Open **Config** and change its display name to `FW-ASA1`.
4. Close the device window.

### Step 3.6B - Connect EDGE-R1 to FW-ASA1

1. Click **Connections > Automatically Choose Connection Type**.
2. Connect `EDGE-R1 GigabitEthernet0/0` to `FW-ASA1 Ethernet0/0`.
3. Press `Esc`.

### Step 3.7 - Place and rename the two core switches

Place these two devices on the same row below `FW-ASA1`.

1. At the bottom-left, click **Network Devices**.
2. Click **Switches**.
3. Find the **3560-24PS** multilayer switch.
4. Click **3560-24PS**.
5. Place it below and slightly left of `FW-ASA1`.
6. Press `Esc`.
7. Click the switch and open **Config**.
8. Change **Display Name** to `CORE-SW1`.
9. Close the window.
10. Click **Network Devices > Switches > 3560-24PS** again.
11. Place the second switch below and slightly right of `FW-ASA1`, level with `CORE-SW1`.
12. Press `Esc`.
13. Click the second switch and open **Config**.
14. Change **Display Name** to `CORE-SW2`.
15. Close the window.

The layout should resemble:

```text
             EDGE-R1
                |
             FW-ASA1
              /   \
       CORE-SW1---CORE-SW2
```

### Step 3.8 - Connect FW-ASA1 to both core switches

Create the left connection:

1. Click **Connections > Automatically Choose Connection Type**.
2. Click `FW-ASA1` and select `Ethernet0/1`.
3. Click `CORE-SW1` and select `FastEthernet0/24`.
4. Press `Esc`.

Create the right connection:

5. Click **Connections > Automatically Choose Connection Type**.
6. Click `FW-ASA1` and select `Ethernet0/2`.
7. Click `CORE-SW2` and select `FastEthernet0/24`.
8. Press `Esc`.

### Step 3.9 - Connect the core switches to each other

1. Click **Connections > Automatically Choose Connection Type**.
2. Click `CORE-SW1` and select `GigabitEthernet0/1`.
3. Click `CORE-SW2` and select `GigabitEthernet0/1`.
4. Press `Esc`.
5. The horizontal core-to-core cable will later become an 802.1Q trunk.

### Step 3.10 - Place and rename the five access switches

All access switches use the **2960-24TT** model. Place them in one row below the two core switches, from left to right in the order shown.

For each row in the following table, repeat the placement procedure below.

| Placement order | Display name | Position below the cores |
|---:|---|---|
| 1 | SW-MKT | Far left |
| 2 | SW-SALES-A | Left-centre |
| 3 | SW-SALES-B | Centre |
| 4 | SW-SUPPORT | Right-centre |
| 5 | SW-SERVER | Far right |

Placement procedure for each switch:

1. Click **Network Devices**.
2. Click **Switches**.
3. Click **2960-24TT**.
4. Move to the required position in the access-switch row.
5. Click once to place the switch.
6. Press `Esc`.
7. Click the switch and open **Config**.
8. Enter the exact display name from the table.
9. Close the device window.
10. Repeat until all five switches are visible.

### Step 3.11 - Connect SW-MKT to both core switches

1. Click **Connections > Automatically Choose Connection Type**.
2. Click `SW-MKT` and select `GigabitEthernet0/1`.
3. Click `CORE-SW1` and select `FastEthernet0/1`.
4. Press `Esc`.
5. Click **Connections > Automatically Choose Connection Type** again.
6. Click `SW-MKT` and select `GigabitEthernet0/2`.
7. Click `CORE-SW2` and select `FastEthernet0/1`.
8. Press `Esc`.

### Step 3.12 - Connect SW-SALES-A to both core switches

1. Connect `SW-SALES-A GigabitEthernet0/1` to `CORE-SW1 FastEthernet0/2`.
2. Connect `SW-SALES-A GigabitEthernet0/2` to `CORE-SW2 FastEthernet0/2`.
3. Use **Connections > Automatically Choose Connection Type** for each cable.
4. Press `Esc` after each cable.

### Step 3.13 - Connect SW-SALES-B to both core switches

1. Connect `SW-SALES-B GigabitEthernet0/1` to `CORE-SW1 FastEthernet0/3`.
2. Connect `SW-SALES-B GigabitEthernet0/2` to `CORE-SW2 FastEthernet0/3`.
3. Use **Connections > Automatically Choose Connection Type** for each cable.
4. Press `Esc` after each cable.

### Step 3.14 - Connect SW-SUPPORT to both core switches

1. Connect `SW-SUPPORT GigabitEthernet0/1` to `CORE-SW1 FastEthernet0/4`.
2. Connect `SW-SUPPORT GigabitEthernet0/2` to `CORE-SW2 FastEthernet0/4`.
3. Use **Connections > Automatically Choose Connection Type** for each cable.
4. Press `Esc` after each cable.

### Step 3.15 - Connect SW-SERVER to both core switches

1. Connect `SW-SERVER GigabitEthernet0/1` to `CORE-SW1 FastEthernet0/5`.
2. Connect `SW-SERVER GigabitEthernet0/2` to `CORE-SW2 FastEthernet0/5`.
3. Use **Connections > Automatically Choose Connection Type** for each cable.
4. Press `Esc` after each cable.

### Step 3.16 - Place and rename the 20 Marketing PCs

Place these PCs below `SW-MKT` in two neat rows of ten.

1. Click **End Devices**.
2. Click the **PC** icon.
3. Click below `SW-MKT` to place the first PC.
4. Continue clicking until 20 PCs have been placed. Packet Tracer keeps the PC tool selected.
5. Press `Esc` after the twentieth PC.
6. Click the first PC and open **Config**.
7. Change **Display Name** to `MKT-PC01`.
8. Close the window.
9. Rename the remaining PCs consecutively as `MKT-PC02`, `MKT-PC03` and so on through `MKT-PC20`.
10. Check that no number is missing or duplicated.

### Step 3.17 - Connect every Marketing PC

For every connection, click **Connections > Automatically Choose Connection Type**, select the PC's `FastEthernet0` port, then select the listed `SW-MKT` port.

| PC | SW-MKT port | PC | SW-MKT port |
|---|---|---|---|
| MKT-PC01 | Fa0/1 | MKT-PC11 | Fa0/11 |
| MKT-PC02 | Fa0/2 | MKT-PC12 | Fa0/12 |
| MKT-PC03 | Fa0/3 | MKT-PC13 | Fa0/13 |
| MKT-PC04 | Fa0/4 | MKT-PC14 | Fa0/14 |
| MKT-PC05 | Fa0/5 | MKT-PC15 | Fa0/15 |
| MKT-PC06 | Fa0/6 | MKT-PC16 | Fa0/16 |
| MKT-PC07 | Fa0/7 | MKT-PC17 | Fa0/17 |
| MKT-PC08 | Fa0/8 | MKT-PC18 | Fa0/18 |
| MKT-PC09 | Fa0/9 | MKT-PC19 | Fa0/19 |
| MKT-PC10 | Fa0/10 | MKT-PC20 | Fa0/20 |

Connection example for `MKT-PC01`:

1. Click **Connections > Automatically Choose Connection Type**.
2. Click `MKT-PC01`.
3. Select `FastEthernet0`.
4. Click `SW-MKT`.
5. Select `FastEthernet0/1`.
6. Press `Esc`.
7. Repeat the same six actions for each remaining row in the table.

### Step 3.18 - Place and rename the 25 Sales PCs

Place `SALES-PC01` to `SALES-PC24` below `SW-SALES-A`. Place `SALES-PC25` below `SW-SALES-B`.

1. Click **End Devices > PC**.
2. Place 24 PCs below `SW-SALES-A`, preferably in three rows of eight.
3. Place one more PC below `SW-SALES-B`.
4. Press `Esc`.
5. Rename the first 24 PCs consecutively from `SALES-PC01` through `SALES-PC24`.
6. Rename the PC below `SW-SALES-B` as `SALES-PC25`.
7. Check the names before connecting them.

### Step 3.19 - Connect every Sales PC

Connect each PC's `FastEthernet0` port to the specified switch port.

| PCs | Access switch | Switch ports |
|---|---|---|
| SALES-PC01 to SALES-PC24 | SW-SALES-A | Fa0/1 to Fa0/24 in matching numerical order |
| SALES-PC25 | SW-SALES-B | Fa0/1 |

Use this exact pattern:

1. `SALES-PC01 FastEthernet0` to `SW-SALES-A FastEthernet0/1`.
2. `SALES-PC02 FastEthernet0` to `SW-SALES-A FastEthernet0/2`.
3. Continue matching the PC number to the port number.
4. Finish with `SALES-PC24 FastEthernet0` to `SW-SALES-A FastEthernet0/24`.
5. Connect `SALES-PC25 FastEthernet0` to `SW-SALES-B FastEthernet0/1`.
6. Use **Automatically Choose Connection Type** for every cable.

### Step 3.20 - Place and rename the 15 Support PCs

1. Click **End Devices > PC**.
2. Place 15 PCs below `SW-SUPPORT`, preferably in three rows of five.
3. Press `Esc`.
4. Rename them consecutively from `SUP-PC01` through `SUP-PC15`.
5. Check that the numbering is complete.

### Step 3.21 - Connect every Support PC

1. Connect `SUP-PC01 FastEthernet0` to `SW-SUPPORT FastEthernet0/1`.
2. Connect `SUP-PC02 FastEthernet0` to `SW-SUPPORT FastEthernet0/2`.
3. Continue matching each PC number to its switch port number.
4. Finish with `SUP-PC15 FastEthernet0` to `SW-SUPPORT FastEthernet0/15`.
5. Use **Connections > Automatically Choose Connection Type** for every cable.

### Step 3.22 - Place and rename the eight internal servers

Place the internal servers below `SW-SERVER`. The first six are normal server VLAN devices. The final two are backup VLAN devices.

1. Click **End Devices**.
2. Click **Server**.
3. Place eight servers below `SW-SERVER` in two rows of four.
4. Press `Esc`.
5. Rename them exactly as shown, keeping this order:

| Placement order | Display name | Role |
|---:|---|---|
| 1 | INFRA-SRV | DHCP and DNS |
| 2 | MAIL-SRV | E-mail |
| 3 | INTRANET-SRV | Internal website |
| 4 | MKT-DB-SRV | Marketing database portal |
| 5 | SALES-DB-SRV | Sales database portal |
| 6 | SUPPORT-DB-SRV | Support database portal |
| 7 | BACKUP-DB-SRV | Backup database |
| 8 | ARCHIVE-SRV | Archive service |

For each server, click it, open **Config**, change **Display Name**, and close the window.

### Step 3.23 - Connect the internal servers

Use **Automatically Choose Connection Type**. On every server, select `FastEthernet0`.

| Server | SW-SERVER port | Later VLAN |
|---|---|---:|
| INFRA-SRV | Fa0/1 | 40 |
| MAIL-SRV | Fa0/2 | 40 |
| INTRANET-SRV | Fa0/3 | 40 |
| MKT-DB-SRV | Fa0/4 | 40 |
| SALES-DB-SRV | Fa0/5 | 40 |
| SUPPORT-DB-SRV | Fa0/6 | 40 |
| BACKUP-DB-SRV | Fa0/7 | 60 |
| ARCHIVE-SRV | Fa0/8 | 60 |

Complete the table from top to bottom, creating one cable at a time.

### Step 3.24 - Place, rename and connect the Admin PC

1. Click **End Devices**.
2. Click **PC**.
3. Place one PC near the internal servers, below `SW-SERVER`.
4. Press `Esc`.
5. Click the PC and open **Config**.
6. Change **Display Name** to `ADMIN-PC`.
7. Close the window.
8. Click **Connections > Automatically Choose Connection Type**.
9. Click `ADMIN-PC` and select `FastEthernet0`.
10. Click `SW-SERVER` and select `FastEthernet0/9`.
11. Press `Esc`.

### Step 3.25 - Add the topology labels with Place Note

Add these labels before configuration so the logical topology clearly shows the building areas, VLANs and WAN links.

1. Click the **Place Note** tool in the Packet Tracer common tools toolbar.
2. Click above `INTERNET-SRV` and enter `Internet / External Network`.
3. Click beside the `INTERNET-SRV` to `ISP-R1` link and enter `Public LAN - 198.51.100.0/24`.
4. Click beside the `ISP-R1` to `EDGE-R1` link and enter `WAN Link - 203.0.113.0/30`.
5. Click beside `EDGE-R1` and enter `MSS Ltd Edge Router - NAT and Default Route`.
6. Click beside `FW-ASA1` and enter `Stateful Perimeter Firewall - Inside/Outside Inspection`.
7. Click beside the `EDGE-R1` to `FW-ASA1` link and enter `10.255.1.0/30`.
8. Click beside both firewall-to-core links and enter `Inside Transit - 10.255.0.0/29`.
9. Click above the two core switches and enter `Core / Distribution Layer - HSRP, STP and Inter-VLAN Routing`.
10. Click beside the core-to-core link and enter `802.1Q Core Trunk - VLANs 10,20,30,40,50,60,99`.
11. Click above the Marketing area and enter `Floor 1 - Marketing - VLAN 10 - 192.168.10.0/27`.
12. Click above both Sales switches and enter `Floor 2 - Sales - VLAN 20 - 192.168.20.0/26`.
13. Click above the Support area and enter `Floor 3 - General Support - VLAN 30 - 192.168.30.0/27`.
14. Click above `SW-SERVER` and enter `Server Room / Server Farm`.
15. Click beside the production servers and enter `Servers - VLAN 40 - 192.168.40.0/27`.
16. Click beside `ADMIN-PC` and enter `Management - VLAN 50 - 192.168.50.0/28`.
17. Click beside `BACKUP-DB-SRV` and `ARCHIVE-SRV` and enter `Protected Backup Network - VLAN 60 - 192.168.60.0/28`.
18. Click near the access-switch uplinks and enter `Dual 802.1Q Trunks - Primary and Redundant Paths`.
19. Click near the unused access-switch ports and enter `Native / Unused VLAN 99 - No User Hosts`.
20. Drag notes as needed so they do not cover devices, cables or port indicators.
21. Press `Ctrl+S` to save the labelled topology.

### Step 3.26 - Check every physical connection before configuration

Inspect the topology and confirm all of the following:

1. `INTERNET-SRV Fa0` connects to `ISP-R1 G0/1`.
2. `ISP-R1 G0/0` connects to `EDGE-R1 G0/2`.
3. `EDGE-R1 G0/0` connects to `FW-ASA1 E0/0`.
4. `FW-ASA1 E0/1` connects to `CORE-SW1 Fa0/24`.
5. `FW-ASA1 E0/2` connects to `CORE-SW2 Fa0/24`.
6. `CORE-SW1 Gi0/1` connects to `CORE-SW2 Gi0/1`.
7. Every access switch has one uplink to each core switch.
8. Marketing PCs use `SW-MKT Fa0/1-20`.
9. Sales PCs 01-24 use `SW-SALES-A Fa0/1-24`.
10. Sales PC 25 uses `SW-SALES-B Fa0/1`.
11. Support PCs use `SW-SUPPORT Fa0/1-15`.
12. Normal servers use `SW-SERVER Fa0/1-6`.
13. Backup servers use `SW-SERVER Fa0/7-8`.
14. `ADMIN-PC` uses `SW-SERVER Fa0/9`.
15. No switch port has two cables connected to it.
16. All devices have the exact display names used in this guide.
17. Click **File > Save** or press `Ctrl+S`.

Do not worry if router-facing links remain red at this stage. Router interfaces are administratively down until the later configuration commands apply `no shutdown`. Continue directly to Section 4 and follow the configuration order exactly as written; it brings the switching and routing foundation online before the servers and PCs request network service.

### Step 3.27 - How to enter every Cisco CLI configuration in this guide

Use this procedure whenever a later section says to configure a router or switch:

1. Click the named router or switch on the workspace.
2. Click the **CLI** tab.
3. If the message `Continue with configuration dialog? [yes/no]:` appears, type `no` and press `Enter`.
4. Press `Enter` once more if the CLI remains blank.
5. Wait until a prompt such as `Switch>` or `Router>` appears.
6. Enter the commands in the displayed code block from the first line to the last line, in order.
7. Press `Enter` after every command.
8. You may paste a complete code block into the CLI, but watch for error messages beginning with `%`.
9. If Packet Tracer asks for confirmation while generating an RSA key, accept the stated hostname/domain and continue.
10. When `write memory` is complete, look for `[OK]`.
11. Run the verification commands printed after that device's configuration.
12. Close the device window only after the expected interfaces and settings appear.
13. Press `Ctrl+S` after finishing each device.

Important: text such as `ReplaceEnableSecret`, `ReplaceAdminSecret` and `ReplaceConsoleSecret` is a placeholder. Replace it with passwords selected by the group, and use the same recorded values whenever the guide asks you to log in later.

---

## 4. Configure CORE-SW1

1. Click `CORE-SW1` on the workspace.
2. Open **CLI**.
3. Answer `no` if the initial configuration dialog appears.
4. Wait for the `Switch>` prompt.
5. Enter the following block in order:

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
exits
```

Configure CORE-SW1 trunks and routed edge link:

```cisco
interface range fastEthernet 0/1-5
 description ACCESS-SWITCH-TRUNK
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 10,20,30,40,50,60,99
 no shutdown
exit

interface gigabitEthernet 0/1
 description CORE-SW2-TRUNK
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 10,20,30,40,50,60,99
 no shutdown
exit

interface fastEthernet 0/24
 description ROUTED-LINK-TO-FW-ASA1-E0/1
 no switchport
 ip address 10.255.0.2 255.255.255.248
 standby 70 ip 10.255.0.4
 standby 70 priority 110
 standby 70 preempt
 no shutdown
exit

ip route 0.0.0.0 0.0.0.0 10.255.0.1
end
write memory
```

---

## 5. Configure CORE-SW2

1. Click `CORE-SW2` on the workspace.
2. Open **CLI**.
3. Answer `no` if the initial configuration dialog appears.
4. Wait for the `Switch>` prompt.
5. Enter the following block in order:

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
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 10,20,30,40,50,60,99
 no shutdown
exit

interface gigabitEthernet 0/1
 description CORE-SW1-TRUNK
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 10,20,30,40,50,60,99
 no shutdown
exit

interface fastEthernet 0/24
 description ROUTED-LINK-TO-FW-ASA1-E0/2
 no switchport
 ip address 10.255.0.3 255.255.255.248
 standby 70 ip 10.255.0.4
 standby 70 priority 100
 standby 70 preempt
 no shutdown
exit

ip route 0.0.0.0 0.0.0.0 10.255.0.1
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

For each access switch, click the switch, open **CLI**, answer `no` to the initial configuration dialog if it appears, and wait for `Switch>`. Enter the common block first. Replace `SWITCH-NAME` with the exact name of the switch currently open. Without closing the CLI, enter that switch's individual block from Steps 6.1 to 6.5. Run the check commands, save, close the window, and then move to the next access switch from left to right.

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

## 7. Configure FW-ASA1 And EDGE-R1

### 7.1 Configure FW-ASA1

1. Click `FW-ASA1`, open **CLI**, and answer `no` to an initial setup dialog.
2. Enter the configuration below. The firewall permits sessions initiated from the trusted inside and denies unsolicited sessions initiated from outside.

```cisco
enable
configure terminal
hostname FW-ASA1
domain-name mss.local
enable password ReplaceEnableSecret
username netadmin password ReplaceAdminSecret privilege 15

interface vlan 1
 nameif inside
 security-level 100
 ip address 10.255.0.1 255.255.255.248
 no shutdown
exit
interface vlan 2
 nameif outside
 security-level 0
 ip address 10.255.1.2 255.255.255.252
 no shutdown
exit

interface ethernet 0/0
 description TO-EDGE-R1-G0/0
 switchport access vlan 2
 no shutdown
exit
interface ethernet 0/1
 description TO-CORE-SW1-FA0/24
 switchport access vlan 1
 no shutdown
exit
interface ethernet 0/2
 description TO-CORE-SW2-FA0/24
 switchport access vlan 1
 no shutdown
exit

route outside 0.0.0.0 0.0.0.0 10.255.1.1 1
route inside 192.168.0.0 255.255.0.0 10.255.0.4 1

access-list OUTSIDE-IN extended deny ip any 192.168.0.0 255.255.0.0
access-group OUTSIDE-IN in interface outside
policy-map global_policy
 class inspection_default
  inspect icmp
exit
service-policy global_policy global

crypto key generate rsa modulus 1024
aaa authentication ssh console LOCAL
ssh 192.168.50.0 255.255.255.240 inside
write memory
```

Check the firewall:

```cisco
show interface ip brief
show route
show access-list
show service-policy
show conn
```

If the Packet Tracer ASA image rejects an advanced inspection command, keep the inside/outside interfaces, security levels, static routes and outside ACL, then document the simulator limitation.

### 7.2 Configure EDGE-R1

1. Click `EDGE-R1` on the workspace.
2. Open **CLI**.
3. Answer `no` if the initial configuration dialog appears.
4. Wait for the `Router>` prompt.
5. Enter the following block in order:

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
 description TO-FW-ASA1-OUTSIDE
 ip address 10.255.1.1 255.255.255.252
 ip nat inside
 no shutdown
exit

interface gigabitEthernet 0/1
 description UNUSED
 shutdown
exit

interface gigabitEthernet 0/2
 description TO-ISP-R1
 ip address 203.0.113.2 255.255.255.252
 ip nat outside
 no shutdown
exit

ip route 192.168.0.0 255.255.0.0 10.255.1.2
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

1. Click `ISP-R1` on the workspace.
2. Open **CLI**.
3. Answer `no` if the initial configuration dialog appears.
4. Wait for the `Router>` prompt.
5. Enter the following block in order:

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
- Default route points to `FW-ASA1` at `10.255.0.1`.

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

On FW-ASA1:

```cisco
show conn
show access-list
show service-policy
```

Expected result:

- External web page loads.
- NAT translations appear on EDGE-R1.
- The ASA connection table shows the inside-initiated session and unsolicited inbound traffic remains denied.

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

### Step 13.3 - Test firewall-to-core route resilience

1. Confirm `FW-ASA1` routes the internal summary through transit HSRP address `10.255.0.4`:

```cisco
show route
```

2. Shut down the active core's firewall-facing link or power off that core.
3. Test Internet access again.
4. Restore the link.

Expected result: HSRP group 70 moves `10.255.0.4` to the surviving core and Internet access recovers after reconvergence.

---

## 14. Troubleshooting Order

Use this order whenever something fails:

1. Cable - Check the cable is connected to the intended port.
2. Interface - Run `show interfaces status`.
3. VLAN - Run `show vlan brief`.
4. Trunk - Run `show interfaces trunk`.
5. SVI - Run `show ip interface brief`.
6. DHCP - Check DHCP pool, helper address, server gateway and VLAN.
7. Firewall - On `FW-ASA1`, check `show interface ip brief`, `show route`, `show conn`, `show access-list` and `show service-policy`.
8. Gateway - Confirm the PC uses the HSRP `.1` gateway.
9. Routing - Run `show ip route` on core switches and routers.
10. ACL - Run `show access-lists`.
11. NAT - Run `show ip nat translations` and `show ip nat statistics`.
12. DNS - Ping by IP first, then ping by DNS name.
13. STP/HSRP - Run `show spanning-tree` and `show standby brief`.

Common fixes:

| Symptom | Likely cause | Fix |
|---|---|---|
| PC gets 169.254.x.x | DHCP cannot reach the PC | Check access VLAN, trunks, SVI and helper address |
| SVI is down/down | No active port in that VLAN | Check VLAN membership and trunk status |
| One VLAN fails over a trunk | VLAN missing from allowed list | Add VLAN on both trunk ends |
| DNS name fails but IP works | DNS record or DNS server issue | Check INFRA-SRV DNS and client DNS address |
| NAT table is empty | NAT ACL/interface/default route issue | Check inside/outside roles, ACL 1 and routing |
| Core reaches ASA but not EDGE-R1 | ASA outside interface/default route issue | Check ASA interfaces, security levels and `show route` |
| Return traffic fails at the firewall | Inspection or outside-policy issue | Check `show conn`, `show service-policy` and `OUTSIDE-IN` counters |
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
16. ASA connection table, inspection policy and outside ACL counters.
17. Successful Admin SSH and blocked normal-user SSH.
18. Redundancy test before, during and after failure.
19. Port security and unused port shutdown evidence.
20. Final saved Packet Tracer topology.

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
- [ ] FW-ASA1 permits inside-initiated sessions and denies unsolicited inbound traffic.
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
- FW-ASA1 provides the routed stateful security boundary between the core and edge router.
- The backup VLAN is intentionally protected from normal user VLANs.
- The database servers remain reachable from all departments because the brief requires company-wide database access.
- In production, real database authentication, role-based access control, HTTPS, secure mail, central AAA, next-generation IDS/IPS, syslog, NTP and vulnerability scanning should be added.
- Packet Tracer is a simulation. Its ASA demonstrates interface trust levels, ACLs and basic stateful inspection, but not the complete protection of a production next-generation firewall.

---

## 18. Suggested Build Schedule

| Session | Work |
|---:|---|
| 1 | Place, rename and cable every device |
| 2 | Configure VLANs, trunks and access ports |
| 3 | Configure core SVIs, HSRP, STP and routing |
| 4 | Configure DHCP, DNS, intranet, database pages and e-mail |
| 5 | Configure FW-ASA1, EDGE-R1, ISP-R1, NAT and Internet server |
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
