# Non intended user logon

 Under some circumstances it is only allowed that users
 from country X logon to devices from country X. 
 This query finds logon from users from other countries than X.
 The query requires a property to identify the users from
 country X. In this example a specific Email Address.
## Query

```
let relevant_computers=
DeviceInfo
| where MachineGroup == "My_MachineGroup" 
| summarize make_list(DeviceName);
let relevant_users=
IdentityInfo
| where EmailAddress endswith "@allowed.users"
| summarize make_list(AccountName);
DeviceLogonEvents
| where Timestamp > ago(1d)
| where DeviceName in (relevant_computers)
| where AccountName !in (relevant_users)
| project DeviceName, AccountName
```

## Contributor info

**Contributor:** jan geisbauer
**Contact info:** @janvonkirchheim 
