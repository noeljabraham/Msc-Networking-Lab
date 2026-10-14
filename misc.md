Residual Limitations
“Residual” means risks that remain after your current security and redundancy controls have been applied.
Your design protects the internal network with an ASA firewall, VLANs, ACLs, SSH, port security, HSRP, STP and dual uplinks. However, these limitations remain:
- Only one EDGE-R1: its failure disconnects the company from the Internet.
- Only one ISP connection: an ISP outage removes Internet access.
- Only one FW-ASA1: its failure disconnects the core from the edge, and the simulated ASA is not a full next-generation firewall or IDS/IPS.
- Important services still depend on individual servers.
- HTTP, POP3 and SMTP in Packet Tracer are not strongly encrypted.
- HSRP protects gateways but does not protect every device or service.
- Packet Tracer does not implement real SQL security, backup automation or enterprise monitoring.
