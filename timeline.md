# Investigation Timeline

## Lab 45 – Windows Software Inventory Timeline Reconstruction

| Time | Activity | Evidence |
|---|---|---|
| T1 | Created investigation workspace | `C:\SoftwareTimelineLab` |
| T2 | Enumerated installed 64-bit applications | PowerShell |
| T3 | Enumerated installed 32-bit applications | PowerShell |
| T4 | Selected application for investigation | Software Inventory |
| T5 | Identified application executable | File System |
| T6 | Reviewed executable timestamps | File System |
| T7 | Investigated Windows process creation | Event ID 4688 |
| T8 | Reviewed user/process context | Windows Event Log |
| T9 | Opened Wazuh Discover | Wazuh |
| T10 | Verified endpoint visibility | Wazuh Discover |
| T11 | Searched for application activity | Wazuh Discover |
| T12 | Correlated Windows and Wazuh evidence | Analyst |
| T13 | Reconstructed software activity timeline | Analyst |
| T14 | Documented investigation findings | Analyst |
| T15 | Completed final assessment | Investigation |

---

# Investigation Flow

```text
Create Investigation Workspace
        ↓
Enumerate 64-bit Software
        ↓
Enumerate 32-bit Software
        ↓
Select Application
        ↓
Locate Executable
        ↓
Review File Timestamps
        ↓
Investigate Event ID 4688
        ↓
Identify User Context
        ↓
Open Wazuh Discover
        ↓
Correlate Endpoint Telemetry
        ↓
Construct Timeline
        ↓
Document Findings
        ↓
Final Assessment
```

---

# Timeline Analysis

The investigation began by collecting the installed software inventory from both 64-bit and 32-bit Windows registry locations.

An application of interest was then selected for further analysis. Its executable location and available file timestamps were reviewed to establish temporal context.

Windows process creation events were investigated to determine whether execution evidence was available. Wazuh Discover was then used to correlate endpoint activity and available process telemetry.

The collected evidence was combined to reconstruct the software-related activity timeline.

---

# Analyst Assessment

The timeline demonstrates how software inventory can serve as the starting point for a broader DFIR investigation.

Software presence alone does not establish when an application was installed or whether it was malicious. File metadata, Windows events, user context, process execution, and Wazuh telemetry should be correlated before reaching a final assessment.
