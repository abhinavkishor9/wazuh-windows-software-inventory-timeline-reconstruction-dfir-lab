# Troubleshooting Notes

## Issue 1 – Application Not in 64-bit Inventory

### Problem

The application not present in the initial 64-bit software inventory.

### Explanation

Some applications are registered under the 32-bit Windows uninstall registry location.

### Action

Check the 32-bit location:

```powershell
Get-ItemProperty `
HKLM:\Software\WOW6432Node\Microsoft\Windows\CurrentVersion\Uninstall\* |
Where-Object {$_.DisplayName} |
Select-Object DisplayName, DisplayVersion, Publisher, InstallDate
```

---





---

## Issue 2– Event ID 4688 Not Available

### Problem

No relevant Event ID 4688 process creation event was found.

### Explanation

Process creation auditing may not be enabled, or the relevant event may have already been overwritten.

### Action

Check the Windows Security log and review available Wazuh telemetry.

If process creation logging is unavailable, document it as an evidence limitation.

---



## Issue 3 – Application Appears Suspicious

### Problem

An unfamiliar application has been identified.

### Explanation

An unfamiliar application does not automatically mean that it is malicious.

### Action

Investigate:

- Application name
- Publisher
- Version
- Installation path
- File timestamps
- User context
- Process execution
- Parent process
- Related Windows events
- Wazuh telemetry

Only classify the activity as suspicious when sufficient contextual evidence supports the conclusion.

---

