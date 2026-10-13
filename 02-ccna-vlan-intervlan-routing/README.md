# CCNA Lab: VLANs & Inter-VLAN Routing (Router-on-a-Stick)

> **Status:** Configs are ready to paste. Build the topology in Packet Tracer, save the file as `vlan-intervlan-routing.pkt` in this folder, and add screenshots to [`screenshots/`](screenshots/).

## Objective

Segment a small office into departmental VLANs, route between them with a single router trunk link (802.1Q sub-interfaces), hand out addresses with DHCP, and harden the switches against common Layer-2 attacks.

## Topology

```
                 ┌────────────┐
                 │     R1     │  Cisco 2911
                 │  Gi0/0     │  sub-ifs .10 .20 .30 .99 .999(native)
                 └─────┬──────┘
                       │ 802.1Q trunk
                 Gi0/1 │
                 ┌─────┴──────┐  Gi0/2      Gi0/1 ┌────────────┐
                 │    SW1     ├──────trunk───────┤    SW2     │
                 │  (2960)    │                  │  (2960)    │
                 └─┬───────┬──┘                  └─┬────┬───┬─┘
             Fa0/1 │       │ Fa0/11          Fa0/1 │    │   │ Fa0/21
                 PC1      PC2                    PC3  PC4  SRV1
               VLAN10   VLAN20                 VLAN10 │  VLAN30
                                                  Fa0/11 VLAN20
```

## VLAN & addressing plan

| VLAN | Name | Subnet | Gateway (R1) | Hosts |
|------|------|--------|--------------|-------|
| 10 | SALES | 192.168.10.0/24 | 192.168.10.1 | PC1, PC3 – DHCP |
| 20 | IT | 192.168.20.0/24 | 192.168.20.1 | PC2, PC4 – DHCP |
| 30 | SERVERS | 192.168.30.0/24 | 192.168.30.1 | SRV1 – static 192.168.30.10 |
| 99 | MGMT | 192.168.99.0/24 | 192.168.99.1 | SW1 .11, SW2 .12 |
| 666 | PARKING | – | – | Unused ports (shut) |
| 999 | NATIVE | – | – | Native VLAN on trunks (no hosts) |

**SRV1:** IP 192.168.30.10/24, GW 192.168.30.1, DNS 192.168.30.10. Enable HTTP, HTTPS and DNS services (add an A record `intranet.lab → 192.168.30.10`). These are used in the ACL lab.

## Configuration

| Device | Config |
|--------|--------|
| R1 | [configs/R1.txt](configs/R1.txt) |
| SW1 | [configs/SW1.txt](configs/SW1.txt) |
| SW2 | [configs/SW2.txt](configs/SW2.txt) |

Key design decisions:
- **Router-on-a-stick** – one physical link, one sub-interface per VLAN with `encapsulation dot1Q`.
- **Native VLAN 999** (unused) instead of VLAN 1, and trunks with `switchport nonegotiate` → mitigates VLAN hopping / DTP abuse.
- **Allowed VLAN list** on trunks – only the VLANs that need to cross.
- **Port security** (sticky MAC, max 2, restrict) + **PortFast/BPDU Guard** on access ports → mitigates MAC flooding and rogue switches.
- **Unused ports** shut and moved to a parking VLAN; VLAN 1 SVI disabled.
- **DHCP** on R1 with excluded ranges for gateways/static hosts.

## Verification

Run these and screenshot the output:

| Command | Expected result |
|---------|-----------------|
| `SW1# show vlan brief` | VLANs 10/20/30/99/666/999 present, access ports in the correct VLAN |
| `SW1# show interfaces trunk` | Gi0/1 & Gi0/2 trunking, native 999, allowed 10,20,30,99,999 |
| `R1# show ip interface brief` | Gi0/0 and all sub-interfaces up/up |
| `R1# show ip dhcp binding` | Leases for PC1–PC4 |
| `SW1# show port-security` | Port security enabled on Fa0/1, Fa0/11 |
| PC1 → `ping 192.168.20.x` (PC2) | Success – inter-VLAN routing works |
| PC1 → `tracert 192.168.30.10` | First hop 192.168.10.1, then SRV1 |
| PC2 → `ipconfig` | Address from 192.168.20.11–254 via DHCP |

## Troubleshooting notes

Write down anything that broke and how you fixed it — interviewers love this. Common ones:
- Sub-interface up but no ping → VLAN missing on the switch or not in the trunk allowed list.
- `%CDP-4-NATIVE_VLAN_MISMATCH` → native VLAN differs on the two ends of a trunk.
- PC gets 169.254.x.x → DHCP pool/gateway wrong or the access VLAN isn't on the trunk.

`TODO` – your own issues and fixes.
