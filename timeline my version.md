# Investigation Timeline

## Lab 45 – Windows Software Inventory Timeline Reconstruction

| Step | Activity | Evidence |
|---|---|---|
| 1 | Created lab folder | `C:\SoftwareTimelineLab` |
| 2 | Enumerated installed 64-bit applications | PowerShell |
| 3 | Enumerated installed 32-bit applications | PowerShell |
| 4 | Selected application for investigation | Software Inventory |
| 5 | Investigated Windows process creation | Event ID 4688 |
| 6 | Opened Wazuh Discover | Wazuh |


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
Investigate Event ID 4688
        ↓
Open Wazuh Discover
        ↓


---

