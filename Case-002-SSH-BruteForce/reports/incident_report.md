INCIDENT RESPONSE & FORENSIC TRIAGE REPORT: SSH BRUTE-FORCE ANALYSIS
📌 EXECUTIVE SUMMARY
Incident Classification: Remote Service Password Spraying & Perimeter Compromise (MITRE ATT&CK T1110.001)

Status: Fully Contained & Remediated

Affected Asset: Debian 12 Core Production Server (host@server)

Impact Level: CRITICAL — Automated dictionary spraying bypassed perimeter authentication, granting an adversary an active, unauthorized remote command shell.

🕵️‍♂️ FORENSIC INVESTIGATION & LOG ANALYSIS
Because modern Debian distributions deprecate legacy flat-text log files (such as /var/log/auth.log), this investigation was conducted entirely via the command line by querying the system's centralized binary journal database (systemd-journald).

Quantification of Attack Volume
To determine the scope and aggressiveness of the threat vector, a query was structured to filter the system journal for authentication failures tied specifically to the OpenSSH daemon unit:

Command:
sudo journalctl -u ssh | grep -i "failed password" | wc -l

Telemetry Returned: 1,931 explicit authentication failures.

Analysis: The high density of failed login attempts over a tightly compressed window confirms a machine-driven, automated dictionary attack rather than human error or standard misconfiguration.

Threat Infrastructure Isolation
To identify the source of the rogue traffic, the network data fields within the failed connection packets were isolated and counted:

Command:
sudo journalctl -u ssh | grep -i "failed password" | awk '{print $(NF-3)}' | sort | uniq -c | sort -nr

Attacker Profile: Malicious source IP 192.168.247.133 was responsible for 100% of the 1,931 unauthorized authentication attempts.

Compromise Verification & Timeline Reconstruction
To verify if the threat actor successfully pierced the perimeter shield, the journal database was swept for active cryptographic session validations:

Command:
sudo journalctl -u ssh | grep -i "accepted password"

Evidence Indicators:

Multiple persistent sessions established by malicious source node 192.168.247.133 starting at 14:41:20.

A final compromise connection executed by source node 192.168.247.135 at 15:43:49 targeting account context host.

Impact Assessment: Confirmed Host Compromise. The threat actor successfully guessed the credential parameters for the valid user account "host" and spawned an interactive remote terminal shell, elevating the incident from a scanning attempt to an active system breach.

🛠️ CONTAINMENT & REMEDIATION BLUEPRINT
To completely eliminate the risk of a repeat remote service compromise, the following hardening steps have been designed for implementation:

Disable Password-Based Authentication
Force the SSH daemon to drop password challenges completely, requiring robust cryptographic key-pair verification.

Action: Edit /etc/ssh/sshd_config and update the directive:
PasswordAuthentication no

Impact: Automated brute-force utilities like Hydra are instantly rendered useless, as there are no password entry fields to attack.

Deploy an Intrusion Prevention Framework (Fail2Ban)
Implement an automated script that monitors the systemd journal streams live and dynamically blocks offending IPs at the firewall layer.

Action: Install and configure fail2ban to monitor the SSH jail:
[sshd]
enabled = true
maxretry = 5
bantime = 1h

Impact: Any IP address generating more than 5 failures will automatically have its connection sockets dropped at the Linux kernel level for 1 hour.

Move the Default Listening Port
Obfuscate the entry point to reduce exposure to internet-wide automated port scanners.

Action: Shift the SSH listening port from standard TCP Port 22 to a non-standard high port (e.g., Port 2222 or 4822).
