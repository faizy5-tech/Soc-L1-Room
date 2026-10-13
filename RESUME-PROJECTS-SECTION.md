# CV "Projects / Home Lab" section – copy & paste

Replace the `[N]` placeholders with **your real numbers** from the lab before using this.

---

**PROJECTS / HOME LAB** — github.com/faizy5-tech/Soc-L1-Room

**Azure Sentinel Honeypot & Brute-Force Detection** | Microsoft Sentinel, KQL, Azure
github.com/faizy5-tech/Soc-L1-Room/tree/main/01-sentinel-honeypot-lab
- Deployed an internet-exposed Windows VM honeypot and ingested Security event logs into Microsoft Sentinel via Azure Monitor Agent; captured [N] failed RDP logons from [N] unique IPs across [N] countries in [N] hours.
- Wrote KQL hunting queries and 4 scheduled analytics rules (brute force, brute force → success, password spray, log clearing) mapped to MITRE ATT&CK T1110, T1078 and T1070.
- Enriched attacker IPs with a GeoIP watchlist and built a workbook attack map; triaged incidents and documented response following NIST SP 800-61.

**CCNA Lab – VLANs & Inter-VLAN Routing** | Cisco Packet Tracer, IOS
github.com/faizy5-tech/Soc-L1-Room/tree/main/02-ccna-vlan-intervlan-routing
- Segmented a 2-switch network into 4 VLANs with 802.1Q trunks and router-on-a-stick routing; configured DHCP pools on the router.
- Hardened Layer 2 with port security, BPDU Guard, unused native VLAN, DTP disabled and unused ports shut/parked.

**CCNA Lab – ACLs & Device Hardening** | Cisco Packet Tracer, IOS
github.com/faizy5-tech/Soc-L1-Room/tree/main/03-ccna-acl-security
- Enforced a least-privilege inter-VLAN policy with extended and standard ACLs placed per best practice; validated with an 11-case test matrix and ACL hit counters.
- Restricted device management to SSH v2 from the IT VLAN only, with VTY access-class and login brute-force blocking.

---

### One-liner version (if space is tight)

**Home Lab:** Azure Sentinel honeypot with custom KQL detections & MITRE-mapped analytics rules; Cisco Packet Tracer labs covering VLANs, inter-VLAN routing, ACLs and switch/router hardening — github.com/faizy5-tech/Soc-L1-Room
