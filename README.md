# Blue Team & Incident Response Portfolio
Welcome to my security analysis and threat hunting repository. This space serves as a continuous portfolio documenting hands-on security investigations, digital forensics, and incident triage across simulated enterprise environments. 
My analysis focuses on translating raw endpoint, network, and cloud telemetry into structured, corporate-ready incident reports mapped to industry-standard frameworks.

## 🛠️ Core Technical Skills Matrix
* **Log Analysis & Auditing:** Microsoft Sysmon, Windows Event Forwarding (WEF), ETW Providers.
* **Framework Alignment:** MITRE ATT&CK Matrix, Cyber Kill Chain, NIST SP 800-61.
* **Threat Hunting Focus:** Living-off-the-Land (LotL) detection, process lineage tracking, malicious PowerShell argument parsing.
* **Documentation:** Corporate Incident Triage Reports, Root Cause Analysis (RCA).


## 📂 Active Case Investigations Index

| Case ID | Incident Type | Primary Log Sources | Key Techniques Detected | Status |
| :--- | :--- | :--- | :--- | :--- |
| **[CASE-001](./Case-001-Sysmon-ReverseShell/)** | Interactive C2 Reverse Shell | Sysmon (IDs 1, 3) | Living-off-the-Land, PowerShell `IEX` | **Completed** |
| *CASE-002* | *Upcoming Investigation* | *TBD* | *Credential Dumping / Persistence* | *In Pipeline* |

---

## 🎯 Portfolio Objectives
Each case documented in this repository follows a strict enterprise response cycle:
1. **Simulation:** Executing the attack chain in a controlled virtual environment.
2. **Telemetry Collection:** Configuring granular logging policies (e.g., customized Sysmon configs) to isolate malicious footprints from baseline noise.
3. **Triage & Investigation:** Extracting indicators of compromise (IoCs), reconstructing timelines, and mapping behavioral patterns to MITRE ATT&CK.
4. **Reporting:** Writing professional-grade reports outlining the executive summary, technical root cause, and defensive remediation strategies.

---
📫 **Connect with Me:** https://www.linkedin.com/in/muhammad-saad-012390407/ | saadkh5511@gmail.com
