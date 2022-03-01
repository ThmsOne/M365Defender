# Detect command-and-control communication related to BlueKeep cryptomining

This query was originally published in the threat analytics report, *Exploitation of CVE-2019-0708 (BlueKeep)*.

[CVE-2019-0708](https://nvd.nist.gov/vuln/detail/CVE-2019-0708), also known as BlueKeep, is a critical remote code execution vulnerability involving RDP. Soon after its disclosure, the NSA issued a rare [advisory](https://www.nsa.gov/News-Features/News-Stories/Article-View/Article/1865726/nsa-cybersecurity-advisory-patch-remote-desktop-services-on-legacy-versions-of/) about this vulnerability, out of concern that it could be used to quickly spread malware. Attackers have since used this vulnerability to [install cryptocurrency miners](https://www.wired.com/story/bluekeep-hacking-cryptocurrency-mining/) on targets.

Microsoft has issued [updates](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-0708) for this vulnerability, as well as [guidance](https://support.microsoft.com/en-us/help/4500705/customer-guidance-for-cve-2019-0708) for protecting operating systems that we no longer support. Microsoft Defender ATP also contains [behavioral detections](https://www.microsoft.com/security/blog/2019/11/07/the-new-cve-2019-0708-rdp-exploit-attacks-explained/) for defending against this threat.

The following query locates devices that have communicated with attacker infrastructure associated with BlueKeep-related cryptomining.

## Query

```Kusto
// Suggest setting Timestamp starting from September 6th
// when the BlueKeep Metasploit module was released
let IPs = pack_array("109.176.117.11", "5.100.251.106", 
"217.23.5.20", "5.135.199.19"); 
DeviceNetworkEvents
| where Timestamp > ago(7d) 
| where RemoteIP in(IPs)
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
| Command and control | v |  |
| Exfiltration |  |  |
| Impact |  |  |
| Vulnerability |  |  |
| Misconfiguration |  |  |
| Malware, component |  |  |

## See also

* [Detect BlueKeep-related mining](../Execution/detect-bluekeep-related-mining.md)
* [Detect BlueKeep exploitation attempts](../Initial%20access/detect-bluekeep-exploitation-attempts.md)
* [Detect suspicious RDP activity related to BlueKeep](..\Lateral%20Movement\detect-suspicious-rdp-connections.md)

## Contributor info

**Contributor:** Microsoft Threat Protection team
