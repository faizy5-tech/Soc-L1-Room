# Azure Sentinel Honeypot & Brute-Force Detection Lab

> **Status:** 🛠️ Fill in the `TODO` fields with your own results and screenshots before linking this on your CV. Never publish numbers you did not actually observe — interviewers will ask about them.

## Objective

Deploy a deliberately exposed Windows VM in Azure, collect its security logs in Microsoft Sentinel, and use KQL to detect, enrich, investigate and respond to real-world brute-force attacks — the same workflow a SOC L1 analyst follows on shift.

## Architecture

```
 Internet attackers
        │  RDP 3389 (NSG: allow any – honeypot only)
        ▼
┌──────────────────────┐   Azure Monitor Agent   ┌──────────────────────┐
│ Windows VM (honeypot)│ ──────────────────────► │ Log Analytics        │
│ Windows Defender FW  │   Data Collection Rule  │ Workspace            │
│ disabled             │   (Security events)     └──────────┬───────────┘
└──────────────────────┘                                    │
                                                            ▼
                         ┌───────────────────────────────────────────────┐
                         │ Microsoft Sentinel                            │
                         │  • Watchlist: geoip (IP range → country/lat)  │
                         │  • Analytics rules → Incidents                │
                         │  • Workbook: attack map                       │
                         └───────────────────────────────────────────────┘
```

| Component | Setting |
|-----------|---------|
| Resource group | `RG-SOC-Lab` (one RG so the whole lab can be deleted in one action) |
| VM | Windows 10/11 or Server, size B1s/B2s |
| NSG | Inbound allow **any** (honeypot only — never do this in production) |
| Data connector | *Windows Security Events via AMA*, event set **All Security Events** |
| Workspace | `LAW-SOC-Lab` with Microsoft Sentinel enabled |
| Watchlist | `geoip` – alias `geoip`, search key `network` |

## Build steps

1. Create resource group `RG-SOC-Lab` and a Log Analytics workspace `LAW-SOC-Lab`.
2. Add **Microsoft Sentinel** to the workspace.
3. Deploy the Windows VM; set its NSG to allow all inbound traffic.
4. RDP in, open `wf.msc` and turn **off** the Domain/Private/Public firewall profiles so the VM is discoverable.
5. In Sentinel → Content hub, install **Windows Security Events** and configure *Windows Security Events via AMA* with a Data Collection Rule targeting the VM.
6. Verify ingestion: `SecurityEvent | take 10` returns rows (can take 15–30 min).
7. Upload the GeoIP CSV as a watchlist (`geoip`, search key `network`).
8. Deploy the analytics rules in [`analytics-rules/`](analytics-rules/).
9. Build a workbook map from [`kql/04-attack-map.kql`](kql/04-attack-map.kql).
10. Leave the VM exposed for 24–48 h, then investigate the incidents.
11. **Teardown:** delete `RG-SOC-Lab` to stop costs and remove the exposed host.

## Detections & queries

| File | Purpose | MITRE ATT&CK |
|------|---------|--------------|
| [kql/01-failed-logons-overview.kql](kql/01-failed-logons-overview.kql) | Volume of failed logons by source IP | T1110 |
| [kql/02-top-targeted-usernames.kql](kql/02-top-targeted-usernames.kql) | Which account names attackers guess | T1110.001 / T1110.003 |
| [kql/03-geo-enrichment.kql](kql/03-geo-enrichment.kql) | Attacker IP → country / city via watchlist | – |
| [kql/04-attack-map.kql](kql/04-attack-map.kql) | Workbook map data | – |
| [kql/05-failure-reason-breakdown.kql](kql/05-failure-reason-breakdown.kql) | Decode 4625 `SubStatus` (bad user vs bad password) | T1110 |
| [kql/06-persistence-hunt.kql](kql/06-persistence-hunt.kql) | New users, group adds, scheduled tasks, log clearing | T1136 / T1098 / T1053 / T1070 |
| [analytics-rules/brute-force-attempt.md](analytics-rules/brute-force-attempt.md) | ≥10 failures from one IP in 5 min | T1110.001 |
| [analytics-rules/brute-force-success.md](analytics-rules/brute-force-success.md) | Brute force followed by a successful logon | T1110 + T1078 |
| [analytics-rules/password-spray.md](analytics-rules/password-spray.md) | One IP, many distinct usernames | T1110.003 |
| [analytics-rules/security-log-cleared.md](analytics-rules/security-log-cleared.md) | Event 1102 – defence evasion | T1070.001 |

## Findings (fill in from your own lab)

| Metric | Value |
|--------|-------|
| Exposure window | `TODO` (e.g. 48 h) |
| Total failed logons (4625) | `TODO` |
| Unique attacking IPs | `TODO` |
| Top 3 source countries | `TODO` |
| Top 3 usernames tried | `TODO` |
| Incidents generated | `TODO` |
| Successful attacker logons | `TODO` (hopefully 0) |

Screenshots to add to [`screenshots/`](screenshots/):
- `attack-map.png` – workbook map
- `incident-queue.png` – Sentinel incidents
- `incident-investigation.png` – investigation graph / entity page
- `kql-results.png` – a hunting query with results

## Incident response write-up (NIST SP 800-61)

Document **one** incident end to end — this is what interviewers will ask about.

| Phase | Notes |
|-------|-------|
| **Detection & analysis** | Rule that fired: `TODO`. Source IP `TODO`, country `TODO`, username(s) `TODO`, count `TODO` in `TODO` minutes. Checked IP reputation (AbuseIPDB / VirusTotal): `TODO`. Any 4624 success from the same IP? `TODO`. Verdict: true positive / benign positive. |
| **Containment** | In production: block the IP on the NSG / firewall, disable or reset the targeted account, restrict RDP to a VPN/Bastion. In the lab: `TODO` what you did. |
| **Eradication & recovery** | Confirm no persistence (4720 new user, 4732 group add, 7045 new service). `TODO` |
| **Lessons learned** | Never expose RDP directly; enforce account lockout & MFA; use Azure Bastion / JIT VM access; alert on brute force followed by success. |

## What I learned

- `TODO` – write 3–4 bullets in your own words (e.g. how 4625 SubStatus codes distinguish bad usernames from bad passwords, tuning thresholds to cut false positives, how quickly an exposed host is discovered).
