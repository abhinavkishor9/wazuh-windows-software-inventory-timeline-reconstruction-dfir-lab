# Investigation Notes

## Lab Summary

This investigation focused on reconstructing software-related activity on a Windows endpoint using software inventory information, application file metadata, Windows process creation events, and Wazuh endpoint telemetry.

The investigation moved beyond simply identifying installed applications and focused on establishing the temporal context surrounding an application of interest.

---

## Analyst Methodology

1. Create investigation workspace.
2. Enumerate installed 64-bit applications.
3. Enumerate installed 32-bit applications.
4. Select an application for investigation.
5. Identify the application executable.
6. Review file timestamps.
7. Investigate Windows Event ID 4688.
8. Identify available user and process context.
9. Search the endpoint in Wazuh Discover.
10. Correlate available evidence.
11. Construct the activity timeline.
12. Document findings.

---

## Investigation Scenario

Suppose an analyst discovers unexpected software on a Windows workstation.

The investigator needs to determine:

- When did the software appear?
- Where was the application located?
- When was the executable created or modified?
- Was the application executed?
- Which user was associated with the execution?
- Does Wazuh contain supporting endpoint telemetry?

The investigation combines multiple endpoint artifacts to reconstruct the software activity timeline.

---

## Evidence Collected

### Evidence 1 – Investigation Workspace

Collected:

- `C:\SoftwareTimelineLab`

Finding:

Established a dedicated workspace for the investigation.

---

### Evidence 2 – Software Inventory

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

Finding:

Enumerated applications registered under the 32-bit Windows software location.

---

### Evidence 4 – Application Executable

Collected:

- Application Name
- Executable Path
- Installation Location

Finding:

Identified the executable associated with the application selected for timeline reconstruction.

---

### Evidence 5 – File Timestamps

Command Used

```powershell
Get-Item "C:\Path\To\Application.exe" |
Select-Object Name, CreationTime, LastWriteTime, LastAccessTime
```

Finding:

Collected available file timestamps to provide temporal context.

File timestamps were treated as supporting evidence rather than definitive proof of installation time.

---

### Evidence 6 – Process Creation

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

## DFIR Analysis

Software inventory establishes what applications are present, while file metadata and process creation events provide additional temporal and execution context.

The investigation demonstrated that no single artifact should automatically be treated as proof of software installation or malicious activity. Registry information, file timestamps, Windows events, user context, and Wazuh telemetry should be considered together.

---

## MITRE ATT&CK Mapping

| Tactic | Technique | ID |
|---|---|---|
| Discovery | Software Discovery | T1518 |
| Execution | Command and Scripting Interpreter | T1059 |

---

## Analyst Observations

- Software inventory provides the initial identification of applications.
- Both 64-bit and 32-bit registry locations should be reviewed.
- File timestamps provide useful temporal context.
- Event ID 4688 can provide process execution evidence when auditing is enabled.
- User context can help associate activity with an endpoint account.
- Wazuh provides centralized endpoint telemetry for correlation.
- File timestamps should not automatically be interpreted as installation timestamps.
- An unfamiliar application does not automatically indicate malicious activity.
- Multiple artifacts provide stronger investigative confidence.

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

## Investigation Assessment

The investigation reconstructed software-related activity using multiple Windows and Wazuh evidence sources.

The final assessment should consider:

- Application identity
- Publisher
- Version
- Installation path
- File timestamps
- Process execution
- User context
- Related endpoint activity
- Wazuh telemetry

---

## Conclusion

The investigation demonstrated how software inventory can be combined with file metadata, Windows process creation events, and Wazuh telemetry to reconstruct software-related endpoint activity. The approach provides stronger forensic context than relying on software inventory alone.
