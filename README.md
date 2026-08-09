# Windows Software Inventory Timeline Reconstruction with Wazuh (DFIR Lab 45)

## Overview

Software inventory provides a snapshot of applications installed on a Windows endpoint. During a forensic investigation, this information can be combined with file timestamps, Windows process creation events, and Wazuh telemetry to reconstruct when software-related activity occurred.

In this lab, Windows software inventory was collected using PowerShell, an application was selected for further investigation, supporting file and process evidence was reviewed, and endpoint telemetry was correlated through Wazuh Discover.

---

# Lab Objectives

- Understand software inventory from a DFIR perspective
- Enumerate installed Windows applications
- Identify an application requiring further investigation
- Examine application file timestamps
- Investigate Windows process creation activity
- Identify user and process context
- Correlate endpoint activity with Wazuh Discover
- Construct a software activity timeline
- Document forensic observations

---

# Lab Environment

| Component          | Value                                      |
| ------------------ | ------------------------------------------ |
| Host OS            | Windows 11 Pro                             |
| SIEM               | Wazuh 4.12                                 |
| Endpoint Agent     | Wazuh Agent                                |
| Endpoint Name      | DESKTOP-9MMM37V                            |
| Agent ID           | 001                                        |
| Investigation Type | Software Inventory Timeline Reconstruction |
| Tools Used         | PowerShell, Event Viewer, Wazuh Discover   |

---

# Tools Used

- PowerShell
- Windows Registry
- Windows Event Viewer
- Windows Security Event Log
- Wazuh Discover
- Wazuh Agent

---

# Investigation Steps

### Step 1

Create the investigation workspace.

```powershell
New-Item -Path "C:\SoftwareTimelineLab" -ItemType Directory -Force
```

---

### Step 2

Enumerate installed 64-bit applications.

```powershell
Get-ItemProperty `
HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\* |
Where-Object {$_.DisplayName} |
Select-Object DisplayName, DisplayVersion, Publisher, InstallDate
```

---

### Step 3

Enumerate installed 32-bit applications.

```powershell
Get-ItemProperty `
HKLM:\Software\WOW6432Node\Microsoft\Windows\CurrentVersion\Uninstall\* |
Where-Object {$_.DisplayName} |
Select-Object DisplayName, DisplayVersion, Publisher, InstallDate
```

---

### Step 4

Select an application for investigation.

Review:

- Application Name
- Display Version
- Publisher
- Installation Date

Select an unfamiliar, unexpected, or otherwise interesting application for timeline analysis.

---

### Step 5

Locate the application executable.

Check common installation locations:

```powershell
Get-ChildItem "C:\Program Files" -Recurse -ErrorAction SilentlyContinue |
Where-Object {$_.Name -like "*ApplicationName*"}
```

Also check:

```powershell
Get-ChildItem "C:\Program Files (x86)" -Recurse -ErrorAction SilentlyContinue |
Where-Object {$_.Name -like "*ApplicationName*"}
```

---

### Step 6

Review executable timestamps.

```powershell
Get-Item "C:\Path\To\Application.exe" |
Select-Object Name, CreationTime, LastWriteTime, LastAccessTime
```

These timestamps provide temporal context for the investigation.

---

### Step 7

Investigate Windows process creation activity.

```powershell
Get-WinEvent -FilterHashtable @{
    LogName='Security'
    Id=4688
} -MaxEvents 100
```

Search for the investigated application:

```powershell
Get-WinEvent -FilterHashtable @{
    LogName='Security'
    Id=4688
} -MaxEvents 500 |
Where-Object {$_.Message -match "ApplicationName"}
```

---

### Step 8

Open Wazuh Discover.

Verify the endpoint:

```text
agent.name: DESKTOP-9MMM37V
```

Search for the investigated application:

```text
agent.name: DESKTOP-9MMM37V AND ApplicationName
```

Where available, review process creation telemetry:

```text
agent.name: DESKTOP-9MMM37V AND data.win.system.eventID:4688
```

---

# Key Findings

- Installed software was enumerated from Windows registry locations.
- Both 64-bit and 32-bit software locations were reviewed.
- An application was selected for further timeline investigation.
- Application file timestamps were examined for temporal context.
- Windows Event ID 4688 was reviewed for process execution evidence.
- User and process context were considered during analysis.
- Wazuh Discover was used to correlate endpoint telemetry.
- Multiple evidence sources were combined to reconstruct software-related activity.

---

# Evidence Correlation

| Evidence | Source | Observation |
|---|---|---|
| Software Inventory | Windows Registry | Installed applications identified |
| Application Details | PowerShell | Name, version, publisher, and install date reviewed |
| Executable | File System | Application path identified |
| File Timestamps | File System | Creation and modification times reviewed |
| Process Execution | Security Event 4688 | Application execution investigated |
| User Context | Windows/Wazuh | Account associated with activity reviewed |
| Endpoint Telemetry | Wazuh Discover | Supporting activity correlated |
| Timeline | Analyst | Software activity reconstructed |

---

# DFIR Value

Software inventory alone shows what is installed, but timeline reconstruction provides additional investigative context.

Combining:

- Software inventory
- Registry information
- File timestamps
- Process creation events
- User context
- Wazuh telemetry

helps investigators determine when software-related activity occurred and whether the activity requires further investigation.

---

# Skills Practiced

- Windows DFIR
- Software Inventory Analysis
- Registry Analysis
- File Timestamp Analysis
- Process Creation Analysis
- Event ID 4688 Analysis
- Timeline Reconstruction
- PowerShell
- Wazuh Discover
- Evidence Correlation
- Endpoint Investigation
- DFIR Documentation

---

# MITRE ATT&CK Context

Software inventory and execution analysis can support several areas of endpoint investigation.

Relevant ATT&CK techniques include:

- **T1518 – Software Discovery**
- **T1059 – Command and Scripting Interpreter**

Where process execution is identified, additional ATT&CK mapping should be based on the actual application and activity observed rather than assuming malicious behavior.

---

# Outcome

Successfully reconstructed software-related endpoint activity by combining Windows software inventory, application file metadata, process creation evidence, user context, and Wazuh telemetry.

The investigation demonstrated how multiple endpoint artifacts can be correlated to construct a timeline and determine whether observed software activity requires further investigation.
