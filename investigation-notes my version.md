# Investigation Notes

## Lab Summary

This investigation focused on reconstructing software-related activity on a Windows endpoint using software inventory information, application file metadata, Windows process creation events, and Wazuh endpoint telemetry.


---

## Analyst Methodology

1. Create lab folder.
2. Enumerate installed 64-bit applications.
3. Enumerate installed 32-bit applications.
4. Select an application for investigation.
5. Identify the application executable.
6. Investigate Windows Event ID 4688 using PowerShell
7. Search the endpoint in Wazuh Discover.


---

## Investigation Scenario


Imagine an analyst receives an alert:

"An unexpected application was discovered on a Windows workstation."

The analyst needs to determine:

When did the software appear?
Was it already present?
Was it recently installed?
Which user was active?
Was there process execution around the same time?
Were there related Windows events?
Does Wazuh contain supporting telemetry?

---

## Evidence Collected

### Evidence 1 

Collected:

- `C:\SoftwareTimelineLab`

Finding:

 workspace for the investigation.

---

### Evidence 2 

Command Used

```powershell
Get-ItemProperty `
HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\* |
Where-Object {$_.DisplayName} |
Select-Object DisplayName, DisplayVersion, Publisher, InstallDate
```

Finding:

Enumerated installed applications and collected available software metadata.

---

### Evidence 3 – 32-bit Software Inventory

Command Used

```powershell
Get-ItemProperty `
HKLM:\Software\WOW6432Node\Microsoft\Windows\CurrentVersion\Uninstall\* |
Where-Object {$_.DisplayName} |
Select-Object DisplayName, DisplayVersion, Publisher, InstallDate
```

---

### Evidence 4 

Collected:

- Application Name
- Executable Path
- Installation Location

Finding:

Identified the executable associated with the application selected for timeline reconstruction.

---


---

### Evidence 5

Event Investigated:

- Event ID 4688

Command Used

```powershell
Get-WinEvent -FilterHashtable @{
    LogName='Security'
    Id=4688
} -MaxEvents 100
```

Finding:

Reviewed Windows process creation events for evidence of application execution.

---

### Evidence 7 – Wazuh Endpoint Correlation

Wazuh Discover Search:

```text
agent.name: DESKTOP-9MMM37V
```

Application search:

```text
agent.name: DESKTOP-9MMM37V AND ApplicationName
```

Finding:

Reviewed available endpoint telemetry and correlated software-related activity with the Windows endpoint.

---


## MITRE ATT&CK Mapping

| Tactic | Technique | ID |
|---|---|---|
| Discovery | Software Discovery | T1518 |
| Execution | Command and Scripting Interpreter | T1059 |

---

## Timeline Reconstruction

The timeline should be constructed from the actual timestamps collected during the investigation.

Example structure:

| Time | Activity | Source | Significance |
|---|---|---|---|
| T1 | Application identified | Software Inventory | Software present |
| T2 | Executable located | File System | Installation path identified |
| T3 | File timestamp reviewed | File System | Temporal context |
| T4 | Process execution observed | Event ID 4688 | Execution evidence |
| T5 | User context identified | Windows/Wazuh | Account attribution |
| T6 | Endpoint activity correlated | Wazuh | Supporting evidence |
| T7 | Timeline documented | Analyst | Final assessment |

---

## Conclusion

The investigation demonstrated how software inventory can be combined with file metadata, Windows process creation events, and Wazuh telemetry to reconstruct software-related endpoint activity. The approach provides stronger forensic context than relying on software inventory alone.
