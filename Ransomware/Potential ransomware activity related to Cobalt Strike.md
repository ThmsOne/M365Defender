# Devices with Cobalt Strike and defense evasion alerts

Use this query to look for alerts related to suspected ransomware and Cobalt Strike activity, a tool used in numerous ransomware campaigns

## Query
```
// Look for sc.exe disabling services
AlertInfo 
// Attempts to clear security event logs. 
| where Title in("Event log was cleared", 
// List alerts flagging attempts to delete backup files. 
"File backups were deleted", 
// Potential Cobalt Strike activity - Note that other threat activity can also 
// trigger alerts for suspicious decoded content 
"Suspicious decoded content", 
// Cobalt Strike activity 
"\'Atosev\' malware was detected", 
"\'Ploty\' malware was detected", 
"\'Bynoco\' malware was detected",
"\'Cobaltstrike\' malware was detected",
"Echo command over pipe on localhost",
"Known attack framework activity was observed",
"An active \'Cobaltstrike\' malware was detected",
"Suspicious \'CobaltStrike\' behavior was prevented",
"Suspicious process launch by Rundll32.exe") 
| extend AlertTime = Timestamp | distinct AlertTime, AlertId, Title 
| join AlertEvidence on $left.AlertId == $right.AlertId
| summarize by DeviceId, AlertTime, Title, AlertId
// Get device IDs
| join DeviceLogonEvents on $left.DeviceId == $right.DeviceId 
// Creating 10 day Window surrounding alert activity 
| where Timestamp < AlertTime +5d and Timestamp > AlertTime - 5d // Projecting specific columns 
| project Title, DeviceName, DeviceId, Timestamp, LogonType, AccountDomain, AccountName, AccountSid, AlertTime, AlertId, RemoteIP, RemoteDeviceName

```
## Category
This query can be used to detect the following attack techniques and tactics ([see MITRE ATT&CK framework](https://attack.mitre.org/)) or security configuration states.
| Technique, tactic, or state | Covered? (v=yes) | Notes |
|------------------------|----------|-------|
| Initial access |  |  |
| Execution |  |  |
| Persistence |  |  | 
| Privilege escalation |  |  |
| Defense evasion |  |  | 
| Credential Access |  |  | 
| Discovery |  |  | 
| Lateral movement |  |  | 
| Collection |  |  | 
| Command and control |  |  | 
| Exfiltration |  |  | 
| Impact |  |  |
| Vulnerability |  |  |
| Exploit |  |  |
| Misconfiguration |  |  |
| Malware, component |  |  |
| Ransomware | V |  |


## Contributor info
**Contributor:** Microsoft 365 Defender
