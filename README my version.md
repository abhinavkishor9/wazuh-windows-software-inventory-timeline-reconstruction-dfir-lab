# wazuh-windows-software-inventory-timeline-reconstruction-dfir-lab
## Overview

A software inventory tells us what software exists on the endpoint.

A timeline investigation asks a more useful question:

When did the software change, and what other endpoint activity occurred around that time?

For example:

Software Inventory
        ↓
Identify Interesting Application
        ↓
Find Installation / Registry Evidence
        ↓
Determine Approximate Time
        ↓
Search Windows Events Around Time
        ↓
Correlate Wazuh Telemetry
        ↓
Build Software Activity Timeline

The goal is not to prove that an application is malicious merely because it is unfamiliar.

Instead, we reconstruct the surrounding activity.
In this lab, We collected 64-bit and 32-bit Windows software inventory using PowerShell, an application was selected for further investigation, supporting file and process evidence was reviewed. We used Windows PowerShell, Wazuh Discover and Windows Registry.

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


# MITRE ATT&CK Context

Software inventory and execution analysis can support several areas of endpoint investigation.

Relevant ATT&CK techniques include:

- **T1518 – Software Discovery**
- **T1059 – Command and Scripting Interpreter**

Where process execution is identified, additional ATT&CK mapping should be based on the actual application and activity observed rather than assuming malicious behavior.

---


