# Rule: Brute Force Followed by Successful Logon

| Setting | Value |
|---------|-------|
| Type | Scheduled query rule |
| Severity | **High** |
| MITRE ATT&CK | T1110 Brute Force → T1078 Valid Accounts |
| Run every | 10 minutes |
| Lookup data from last | 1 hour |
| Alert threshold | Number of results > 0 |
| Entity mapping | IP → `IpAddress`; Host → `Computer`; Account → `TargetUserName` |

```kql
let threshold = 10;
let failures =
    SecurityEvent
    | where TimeGenerated > ago(1h)
    | where EventID == 4625
    | where isnotempty(IpAddress) and IpAddress != "-"
    | summarize FailedAttempts = count(), LastFailure = max(TimeGenerated) by IpAddress, Computer
    | where FailedAttempts >= threshold;
let successes =
    SecurityEvent
    | where TimeGenerated > ago(1h)
    | where EventID == 4624 and LogonType in (3, 10)   // network / RemoteInteractive (RDP)
    | where isnotempty(IpAddress) and IpAddress != "-"
    | project SuccessTime = TimeGenerated, IpAddress, Computer, TargetUserName, LogonType;
failures
| join kind=inner successes on IpAddress, Computer
| where SuccessTime > LastFailure
| project SuccessTime, IpAddress, Computer, TargetUserName, LogonType, FailedAttempts
```

**Response:** treat as a likely compromise — isolate the host, disable/reset the account, hunt for persistence with `kql/06-persistence-hunt.kql`, escalate immediately.
