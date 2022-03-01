# Possible Ransomware Related Destruction Activity

This query identifies common processes run by ransomware
malware to destroy volume shadow copies or clean free
space on a drive to prevent a file from being recovered
post-encryption.  To reduce false positives, results are
filtered to only actions taken when the initiating 
process was launched from a suspicious directory.  If 
you don't mind false positives, consider removing the 
last where clause.

Special thanks to Captain for additional inputs

## Query

```
DeviceProcessEvents
| where Timestamp > ago(7d)
| where (FileName =~ 'vssadmin.exe' and ProcessCommandLine has "delete shadows" and ProcessCommandLine has "/all" and ProcessCommandLine has "/quiet" ) // Clearing shadow copies
    or (FileName =~ 'cipher.exe' and ProcessCommandLine contains "/w") // Wiping drive free space
    or (FileName =~ 'schtasks.exe' and ProcessCommandLine has "/change" and ProcessCommandLine has @"\Microsoft\Windows\SystemRestore\SR" and ProcessCommandLine has "/disable") // Disabling system restore task
    or (FileName =~ 'fsutil.exe' and ProcessCommandLine has "usn" and ProcessCommandLine has "deletejournal" and ProcessCommandLine has "/d") // Deleting USN journal
    or (FileName =~ 'icacls.exe' and ProcessCommandLine has @'"C:\*"' and ProcessCommandLine contains '/grant Everyone:F') // Attempts to re-ACL all files on the C drive to give everyone full control
    or (FileName =~ 'powershell.exe' and (
            ProcessCommandLine matches regex @'\s+-((?i)encod?e?d?c?o?m?m?a?n?d?|e|en|enc|ec)\s+' and replace(@'\x00','', base64_decode_tostring(extract("[A-Za-z0-9+/]{50,}[=]{0,2}",0 , ProcessCommandLine))) matches regex @".*(Win32_Shadowcopy).*(.Delete\(\)).*"
        ) or ProcessCommandLine matches regex @".*(Win32_Shadowcopy).*(.Delete\(\)).*"
    ) // This query looks for PowerShell-based commands used to delete shadow copies
```
## Category

This query can be used to detect the following attack techniques and tactics ([see MITRE ATT&CK framework](https://attack.mitre.org/)) or security configuration states.

| Technique, tactic, or state | Covered? (v=yes) | Notes |
|------------------------|----------|-------|
| Initial access |  |  |
| Execution | v |  |
| Persistence |  |  | 
| Privilege escalation |  |  |
| Defense evasion |  |  | 
| Credential Access |  |  | 
| Discovery |  |  | 
| Lateral movement |  |  | 
| Collection |  |  | 
| Command and control |  |  | 
| Exfiltration |  |  | 
| Impact | v |  |
| Vulnerability |  |  |
| Misconfiguration |  |  |
| Malware, component |  |  |


## Contributor info

**Contributor:** Michael Melone, with special thanks to Captain and @kshitijk_

**GitHub alias:** mjmelone

**Organization:** Microsoft

**Contact info:** @PowershellPoet
