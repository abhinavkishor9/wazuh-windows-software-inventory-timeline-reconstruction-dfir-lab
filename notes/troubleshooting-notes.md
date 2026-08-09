# Troubleshooting Notes

## Issue 1 – Application Not Found in 64-bit Inventory

### Problem

The application was not displayed in the initial 64-bit software inventory.

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

## Issue 2 – Application Has No InstallDate

### Problem

The selected application did not contain an `InstallDate` value.

### Explanation

The `InstallDate` field is not guaranteed to exist for every application.

### Action

Use supporting evidence:

- Registry information
- File timestamps
- Windows Event Logs
- Process creation events
- Installer activity
- Wazuh telemetry

Do not treat a missing `InstallDate` as evidence that the application was recently installed.

---

## Issue 3 – File Timestamp Does Not Match Installation Date

### Problem

The executable creation time did not match the software installation date.

### Explanation

Files can be copied, extracted, updated, restored, or modified after installation.

### Action

Treat file timestamps as temporal indicators rather than definitive installation evidence.

Correlate the timestamp with:

- Registry evidence
- Event ID 4688
- Installer activity
- User activity
- Wazuh telemetry

---

## Issue 4 – Event ID 4688 Not Available

### Problem

No relevant Event ID 4688 process creation event was found.

### Explanation

Process creation auditing may not be enabled, or the relevant event may have already been overwritten.

### Action

Check the Windows Security log and review available Wazuh telemetry.

If process creation logging is unavailable, document it as an evidence limitation.

---

## Issue 5 – Application Search Returns No Wazuh Results

### Problem

Searching directly for the application name in Wazuh Discover produced no results.

### Explanation

Wazuh may not be collecting a dedicated software inventory event or process telemetry for the application.

### Action

First verify the endpoint:

```text
agent.name: DESKTOP-9MMM37V
```

Then investigate surrounding Windows telemetry using:

- Event ID
- Process name
- User
- Timestamp
- Parent process
- Related endpoint activity

---

## Issue 6 – Too Many Wazuh Results

### Problem

Searching only by endpoint name produced a large number of events.

### Action

Narrow the search using:

```text
agent.name: DESKTOP-9MMM37V AND ApplicationName
```

Or investigate a specific process creation event:

```text
agent.name: DESKTOP-9MMM37V AND data.win.system.eventID:4688
```

Use the actual application name and event fields available in the investigation.

---

## Issue 7 – Application Appears Suspicious

### Problem

An unfamiliar application was identified.

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

## Analyst Lesson

Timeline reconstruction should not rely on one timestamp or one artifact.

The strongest approach is:

```text
Software Inventory
        ↓
Application Identification
        ↓
File Metadata
        ↓
Windows Events
        ↓
Process Execution
        ↓
User Context
        ↓
Wazuh Correlation
        ↓
Timeline Reconstruction
        ↓
Final Assessment
```
