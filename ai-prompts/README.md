# MSS Ltd Network Project - AI Implementation Prompts Library

This folder contains a complete suite of ready-to-use prompts designed to be given to another AI (such as ChatGPT, Claude 3.5 Sonnet, Gemini 1.5/2.0 Pro, or DeepSeek) to build, configure, verify, or document the MSS Ltd Cisco Packet Tracer coursework network.

---

## 📁 Folder Contents

| File | Purpose | Recommended Use Case |
|---|---|---|
| [`01-SYSTEM-AND-CONTEXT-PROMPT.md`](./01-SYSTEM-AND-CONTEXT-PROMPT.md) | Persona, Simulator Rules & Boundaries | Feed first in a multi-turn chat to calibrate the AI |
| [`02-PHYSICAL-TOPOLOGY-AND-CABLING-PROMPT.md`](./02-PHYSICAL-TOPOLOGY-AND-CABLING-PROMPT.md) | Device Placement & Port-to-Port Wiring | When you need exact click-by-click cabling directions |
| [`03-VLAN-AND-IP-ADDRESSING-PLAN-PROMPT.md`](./03-VLAN-AND-IP-ADDRESSING-PLAN-PROMPT.md) | Subnetting, HSRP Gateways & Static IPs | When you need the AI to explain or calculate addressing |
| [`04-CORE-AND-ACCESS-SWITCHING-PROMPT.md`](./04-CORE-AND-ACCESS-SWITCHING-PROMPT.md) | Core & Access Layer CLI Configurations | Generates copy-paste ready switch configurations |
| [`05-PERIMETER-FIREWALL-AND-NAT-PROMPT.md`](./05-PERIMETER-FIREWALL-AND-NAT-PROMPT.md) | ASA 5505 & Edge Router PAT Configuration | Generates firewall rules and NAT overload configs |
| [`06-SERVICES-AND-ENDPOINTS-PROMPT.md`](./06-SERVICES-AND-ENDPOINTS-PROMPT.md) | DHCP, DNS, Intranet, DB Portals & PCs | Guides configuration of the 8 servers and 60 PCs |
| [`07-SECURITY-HARDENING-AND-ACLS-PROMPT.md`](./07-SECURITY-HARDENING-AND-ACLS-PROMPT.md) | Port Security, SSH, VTY & Access Lists | Generates hardening commands and ACL 101 rules |
| [`08-TESTING-FAILOVER-AND-TROUBLESHOOTING-PROMPT.md`](./08-TESTING-FAILOVER-AND-TROUBLESHOOTING-PROMPT.md) | Test Matrix, HSRP Failover & Diagnostics | Generates test cases, evidence tables and fix guides |
| [`09-VULNERABILITY-ASSESSMENT-PROMPT.md`](./09-VULNERABILITY-ASSESSMENT-PROMPT.md) | 30% Coursework Vulnerability Analysis | Generates Kali tools, vulnerability analysis & report |
| [`MASTER_ALL_IN_ONE_PROMPT.md`](./MASTER_ALL_IN_ONE_PROMPT.md) | **Complete Single-Shot Monolithic Prompt** | **Copy and paste this into any AI in a single turn!** |

---

## 🚀 How to Use These Prompts

### Option A: The "Single-Shot" Fast Method (Recommended for Claude 3.5 Sonnet, GPT-4o, Gemini Pro)
1. Open [`MASTER_ALL_IN_ONE_PROMPT.md`](./MASTER_ALL_IN_ONE_PROMPT.md).
2. Copy the entire file contents.
3. Paste it directly into your AI chat session.
4. The AI will output the complete implementation, device configurations, test cases, and academic justification in one comprehensive response.

### Option B: The "Step-by-Step" Interactive Method (Best for troubleshooting or deep learning)
1. Start a new chat session.
2. Paste [`01-SYSTEM-AND-CONTEXT-PROMPT.md`](./01-SYSTEM-AND-CONTEXT-PROMPT.md) and wait for the AI to acknowledge.
3. Paste [`02-PHYSICAL-TOPOLOGY-AND-CABLING-PROMPT.md`](./02-PHYSICAL-TOPOLOGY-AND-CABLING-PROMPT.md) to receive the wiring instructions.
4. Paste prompts `03` through `09` as you progress through each stage of the Packet Tracer lab.

---

## 💡 Tips for Best Results with AI Assistants
- **Always specify Packet Tracer version**: If an AI gives standard Cisco IOS commands that fail on Packet Tracer (e.g. advanced crypto algorithms), remind it: *"Ensure all syntax is 100% compatible with Cisco Packet Tracer v8.x"*.
- **ASA 5505 Syntax**: In Packet Tracer, the ASA 5505 uses VLAN interfaces (`interface Vlan1` inside, `interface Vlan2` outside) and assigns physical switchports with `switchport access vlan X`. Prompt 05 explicitly enforces this syntax.
- **Save Before Testing**: Always run `write memory` or `copy running-config startup-config` on all switches and routers before simulating link failures.

