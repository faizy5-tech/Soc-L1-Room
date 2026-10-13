# Rule: Windows Security Event Log Cleared

| Setting | Value |
|---------|-------|
| Type | Scheduled query rule |
| Severity | High |
| MITRE ATT&CK | Defense Evasion – T1070.001 Clear Windows Event Logs |
| Run every | 5 minutes |
| Lookup data from last | 5 minutes |
| Alert threshold | Number of results > 0 |
| Entity mapping | Host → `Computer`; Account → `SubjectUserName` |

```kql
SecurityEvent
| where EventID == 1102
| project TimeGenerated, Computer, SubjectUserName, SubjectDomainName, Activity
```

**Test it safely:** on the honeypot VM run `wevtutil cl Security` as admin and confirm an incident is created within ~10 minutes.
