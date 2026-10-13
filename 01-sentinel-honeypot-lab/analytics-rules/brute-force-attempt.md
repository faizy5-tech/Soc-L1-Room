# Rule: RDP / Windows Brute-Force Attempt

| Setting | Value |
|---------|-------|
| Type | Scheduled query rule |
| Severity | Medium |
| MITRE ATT&CK | Credential Access – T1110.001 Password Guessing |
| Run every | 5 minutes |
| Lookup data from last | 5 minutes |
| Alert threshold | Number of results > 0 |
| Event grouping | Trigger an alert for each event |
| Entity mapping | IP → `IpAddress`; Host → `Computer`; Account → `TargetUserName` |
| Incident grouping | Group alerts by IP entity within 24 h |

```kql
SecurityEvent
| where EventID == 4625
| where isnotempty(IpAddress) and IpAddress != "-"
| summarize FailedAttempts = count(),
            TargetUserName = any(TargetUserName),
            StartTime = min(TimeGenerated), EndTime = max(TimeGenerated)
          by IpAddress, Computer
| where FailedAttempts >= 10
```

**Tuning notes:** raise the threshold if internal users with stale saved credentials cause false positives; exclude known scanner/jump-host IPs with a watchlist.

**L1 triage steps:** check IP reputation → check whether the same IP has any 4624 success → check if the target account exists (SubStatus `0xC000006A`) → escalate to L2 if a success or a valid account is involved.
