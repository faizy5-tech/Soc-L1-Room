# CCNA Lab: ACLs & Device Hardening

> **Status:** Builds on [Lab 02](../02-ccna-vlan-intervlan-routing/). Apply the configs below on top of the Lab 02 topology, save as `acl-security.pkt` in this folder, and add screenshots to [`screenshots/`](screenshots/).

## Objective

Enforce a least-privilege security policy between departmental VLANs using standard and extended ACLs, and lock down device management to SSH from the IT VLAN only.

## Security policy

| # | Requirement | Implementation | Placement |
|---|-------------|----------------|-----------|
| 1 | SALES may use DHCP and reach SRV1 **only** for DNS/HTTP/HTTPS. SALES must not initiate traffic to IT, SERVERS or MGMT, but IT can still support SALES PCs. | Extended ACL `SALES_IN` | Inbound on `Gi0/0.10` – **close to the source** |
| 2 | Only IT may reach the switch management VLAN (99). | Standard ACL `MGMT_PROTECT` | Outbound on `Gi0/0.99` – **close to the destination** |
| 3 | Devices are managed by SSH v2 only, only from IT, with brute-force protection. | Standard ACL `VTY_ACCESS` + `access-class` on VTY lines, `login block-for` | R1, SW1, SW2 VTY lines |

**Why this placement?** Extended ACLs match source *and* destination/port, so they go near the source to drop unwanted traffic early. Standard ACLs match only the source, so placing them near the destination avoids blocking that source from everywhere else.

## Configuration

| Device | Config |
|--------|--------|
| R1 | [configs/R1-acl-hardening.txt](configs/R1-acl-hardening.txt) |
| SW1 | [configs/SW1-ssh-hardening.txt](configs/SW1-ssh-hardening.txt) |
| SW2 | [configs/SW2-ssh-hardening.txt](configs/SW2-ssh-hardening.txt) |

Highlights of `SALES_IN`:
- `permit udp any any eq 67` comes first – DHCP Discover is sourced from `0.0.0.0`, so without it SALES PCs lose their leases.
- `established` + `echo-reply` lines let **return** traffic for sessions IT starts pass, while still blocking SALES from starting new connections to IT (a stateless ACL needs this).
- Explicit `permit ip 192.168.10.0 0.0.0.255 any` at the end – otherwise the implicit `deny any` drops everything else.

## Test matrix

Run each test, record the actual result, and screenshot the interesting ones.

| # | From | To | Test | Expected | Actual |
|---|------|----|------|----------|--------|
| 1 | PC1 (SALES) | – | `ipconfig /renew` | Gets 192.168.10.x lease | `TODO` |
| 2 | PC1 (SALES) | SRV1 | Web browser `http://192.168.30.10` | ✅ Page loads | `TODO` |
| 3 | PC1 (SALES) | SRV1 | Browser `http://intranet.lab` (DNS) | ✅ Page loads | `TODO` |
| 4 | PC1 (SALES) | SRV1 | `ping 192.168.30.10` | ❌ Blocked (ICMP not permitted) | `TODO` |
| 5 | PC1 (SALES) | PC2 (IT) | `ping` | ❌ Blocked | `TODO` |
| 6 | PC2 (IT) | PC1 (SALES) | `ping` | ✅ Success (echo-reply permitted) | `TODO` |
| 7 | PC1 (SALES) | SW1 192.168.99.11 | `ssh -l admin` | ❌ Blocked | `TODO` |
| 8 | SRV1 (SERVERS) | SW1 192.168.99.11 | `ping` | ❌ Blocked by `MGMT_PROTECT` | `TODO` |
| 9 | PC2 (IT) | SW1 192.168.99.11 | `ssh -l admin` | ✅ Login succeeds | `TODO` |
| 10 | PC2 (IT) | R1 192.168.20.1 | `telnet` | ❌ Refused (SSH only) | `TODO` |
| 11 | PC4 (IT) | R1 192.168.20.1 | `ssh -l admin` | ✅ Login succeeds | `TODO` |

## Verification commands

| Command | What to look for |
|---------|------------------|
| `R1# show access-lists` | Match counters increasing on the expected lines after each test |
| `R1# show ip interface g0/0.10` | `Inbound access list is SALES_IN` |
| `R1# show ip interface g0/0.99` | `Outgoing access list is MGMT_PROTECT` |
| `R1# show ip ssh` | `SSH Enabled - version 2.0` |
| `R1# show login` | Login block-for settings active |

## Link to SOC work

ACL match counters and denied-connection logs are exactly the kind of network telemetry a SOC analyst correlates with endpoint alerts (e.g. a SALES PC suddenly hitting MGMT VLAN deny entries = possible lateral movement).

## What I learned

`TODO` – 3–4 bullets in your own words.
