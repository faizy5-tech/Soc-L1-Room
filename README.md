# SOC L1 & Network Home Lab Portfolio

Hands-on labs built to practise the day-to-day work of a **SOC Level 1 Analyst** and a **CCNA-level Network Engineer**. Each project folder has the build steps, configs or queries, a verification/test plan, and screenshots of the working result.

## Projects / Home Lab

| # | Project | Skills demonstrated | Tools |
|---|---------|---------------------|-------|
| 01 | [Azure Sentinel Honeypot & Brute-Force Detection](01-sentinel-honeypot-lab/) | Log ingestion, KQL hunting, analytics rules, alert triage, incident response (NIST 800-61), MITRE ATT&CK mapping | Microsoft Sentinel, Log Analytics, Azure VM, NSG, KQL |
| 02 | [VLANs & Inter-VLAN Routing (Router-on-a-Stick)](02-ccna-vlan-intervlan-routing/) | VLAN segmentation, 802.1Q trunking, sub-interfaces, DHCP, switch hardening | Cisco Packet Tracer, IOS |
| 03 | [ACLs & Device Hardening](03-ccna-acl-security/) | Standard vs. extended ACLs, ACL placement, least-privilege traffic policy, SSH-only management | Cisco Packet Tracer, IOS |

## Repository layout

```
.
├── 01-sentinel-honeypot-lab/
│   ├── README.md               # Architecture, build steps, findings, IR write-up
│   ├── kql/                    # Hunting & workbook queries
│   ├── analytics-rules/        # Scheduled detection rules (KQL + settings)
│   └── screenshots/
├── 02-ccna-vlan-intervlan-routing/
│   ├── README.md               # Topology, addressing, verification
│   ├── configs/                # Full running-configs (R1, SW1, SW2)
│   └── screenshots/
├── 03-ccna-acl-security/
│   ├── README.md               # Security policy, ACL design, test matrix
│   ├── configs/
│   └── screenshots/
└── RESUME-PROJECTS-SECTION.md  # Ready-to-paste CV section linking to this repo
```

## About

Aspiring SOC L1 Analyst / Network Engineer. Contact: see my CV / LinkedIn.
