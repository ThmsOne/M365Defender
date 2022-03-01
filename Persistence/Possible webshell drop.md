# Possible Webshell Drop
This query looks for files created by IIS or Apache matching common web page content extensions which
can be used to execute arbitrary code. 

The query uses a throtlling mechanism in an attempt to avoid false positive detections for WebDAV or 
other web-based content management which might run under the context of the webserver process. Consider
increasing the value of MaxFileOperations based on your false positive detection tolerance, or set it 
to -1 to disable this feature.

Additional extensions of interest are listed after ExtensionList. Again, consider including \ excluding
these extensions based on your organization's use and tolerance of potential false positive detections.
## Query
```
let MaxFileOperations = 3; // This will attempt to hide WebDAV publish operations by looking for file operations less than 'x' in a 5 minute period
let MaxAge = ago(7d); // This is how far back the query will search
let ExtensionList = pack_array('asp','aspx','aar','ascx','ashx','asmx','c','cfm','cgi','jsp','jspx','php','pl');//,'exe','dll','js','jar','py','ps1','psm1','cmd','psd1','java','wsf','vbs') Commented ones may cause false positive detection - add at will
let IncludeTemp = false; // whether to include files that contain \temp\ in their path
let PossibleShells = DeviceFileEvents 
| where Timestamp  > MaxAge 
    and InitiatingProcessFileName in~('w3wp.exe','httpd.exe') 
    and (IncludeTemp or FolderPath  !contains @'\temp\')
    and ActionType in ('FileCreated', 'FileRenamed', 'FileModified')
| extend extension = tolower(tostring(split(FileName,'.')[-1]))
    , TimeBin = bin(Timestamp, 5m)
| where extension in (ExtensionList);
PossibleShells
| summarize count() by DeviceId, TimeBin
| where MaxFileOperations == -1 or count_ < MaxFileOperations
| join kind=rightsemi PossibleShells on DeviceId, TimeBin
```
## Category
This query can be used to detect the following attack techniques and tactics ([see MITRE ATT&CK framework](https://attack.mitre.org/)) or security configuration states.
| Technique, tactic, or state | Covered? (v=yes) | Notes |
|------------------------|----------|-------|
| Initial access | v |  |
| Execution | v |  |
| Persistence | v |  | 
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
| Misconfiguration |  |  |
| Malware, component |  |  |

## Contributor info
**Contributor:** Michael Melone
**GitHub alias:** mjmelone
**Organization:** Microsoft
**Contact info:** @PowershellPoet
