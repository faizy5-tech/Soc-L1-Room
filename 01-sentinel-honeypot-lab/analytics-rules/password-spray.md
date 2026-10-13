# Rule: Password Spray From a Single Source

| Setting | Value |
|---------|-------|
| Type | Scheduled query rule |
| Severity | Medium |
| MITRE ATT&CK | T1110.003 Password Spraying |
| Run every | 15 minutes |
| Lookup data from last | 1 hour |
| Alert threshold | Number of results > 0 |
| Entity mapping | IP → `IpAddress`; Host → `Computer` |

```kql
SecurityEvent
| where EventID == 4625
| where isnotempty(IpAddress) and IpAddress != "-"
| summarize DistinctUsers = dcount(TargetUserName),
            Attempts = count(),
            Users = make_set(TargetUserName, 25)
          by IpAddress, Computer
| where DistinctUsers >= 5
```

**Why it matters:** spraying tries a few common passwords against many accounts to stay under per-account lockout thresholds, so a per-account rule alone misses it.
