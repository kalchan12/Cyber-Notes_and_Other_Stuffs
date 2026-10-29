---
title: Incident Response Lifecycle & Digital Forensics
category: Threat and Incident Management
tags:
  - incident-response
  - dfir
  - nist-sp-800-61
  - sans-picerl
  - forensics
  - blue-team
---

# Incident Response Lifecycle & Digital Forensics

## Overview & Foundational Principles

In enterprise cybersecurity, the operational reality is that no defensive perimeter is completely impenetrable. Given sufficient time, resources, and determination, sophisticated threat actors can uncover vulnerabilities or bypass access controls. Consequently, having a structured, battle-tested **Incident Response (IR)** capability is essential for modern organizations.

![Incident Response Operations Center](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/b8f415dc-9d5c-4d01-a692-e3dc1fa2a5af/ir-center-720eabbea.webp)

### What is Incident Response?

**Incident Response (IR)** is the coordinated approach an organization takes to prepare for, detect, analyze, contain, eradicate, and recover from cybersecurity incidents (such as unauthorized intrusions, active ransomware outbreaks, and data exfiltration events).

The overarching objectives of an IR program are:
- **Rapid Response:** Minimizing attacker dwell time through immediate, structured triage.
- **Order Over Chaos:** Executing established playbooks rather than reacting with uncoordinated ad-hoc decisions.
- **Evidence Integrity:** Preserving forensic artifacts according to strict legal and chain-of-custody standards.
- **Continuous Improvement:** Leveraging lessons learned to remediate root causes and reinforce defenses against future intrusions.

### Core Terminology: Event vs. Incident

Distinguishing between routine system events and actual security incidents is the cornerstone of effective security operations:

![Event vs Incident](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/b8f415dc-9d5c-4d01-a692-e3dc1fa2a5af/event-vs-incident-3efc7c48e.webp)

- **Event:** Any observable occurrence within a computer system, application, or network infrastructure.  
  *Examples:* A user authenticating to a file share, a routine server reboot, a successful HTTPS handshake, or an automated backup job completing.
- **Security Incident:** An adverse event—or collection of events—that compromises or poses an imminent threat to the confidentiality, integrity, or availability of systems or data.  
  *Rule of Thumb:* All security incidents are events, but only a minute fraction of observable events constitute an incident.  
  *Examples:* Unauthorized database query attempts, lateral movement via Pass-the-Hash, active ransomware encryption, or distributed denial-of-service (DDoS) traffic overwhelming web clusters.

### Incident Handling Teams: CSIRT, CERT, and SOC

- **SOC (Security Operations Center):** The front-line operations unit providing 24/7 continuous monitoring, alert triage, and initial investigation (Tier 1). When an alert is validated as a high-severity breach, the SOC escalates to specialized response teams.
- **CSIRT (Computer Security Incident Response Team):** A multidisciplinary team of Tier 2/Tier 3 analysts and forensic investigators focused on managing, containing, eradicating, and documenting active security incidents.
- **CERT (Computer Emergency Response Team):** Often synonymous with CSIRT, but frequently designates national, regional, or industry-specific coordinating entities (e.g., US-CERT/CISA).

### The Strategic Value of Preparedness

Without a structured IR plan, organizations facing an active breach invariably experience:
1. **Operational Paralysis:** Unclear chains of command and responsibility disputes during high-stress crises.
2. **Spoliation of Evidence:** Prematurely rebooting systems, deleting corrupted logs, or power-cycling machines, destroying critical volatile RAM evidence.
3. **Extended Dwell Time:** Allowing adversaries uninhibited access to harvest credentials and pivot internally.
4. **Reputational & Regulatory Penalties:** Misleading the public, issuing contradictory statements, or violating statutory notification deadlines.

---

## Incident Response Frameworks

To standardize operational workflows, the cybersecurity industry relies on two primary frameworks: **NIST SP 800-61** and the **SANS PICERL** model.

### 1. NIST SP 800-61 Rev. 2

![NIST Incident Handling Lifecycle](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/6b6b1992-ccf4-4be8-81c7-279bfc11e485/nist-ir-model-1ccc96bc7.webp)

NIST organizes incident handling into four iterative phases:
1. **Preparation:** Proactive controls, policy drafting, tool deployment, and baseline logging.
2. **Detection & Analysis:** Identifying anomalies, validating alerts, and determining intrusion scope.
3. **Containment, Eradication, & Recovery:** Stopping lateral spread, purging adversary artifacts, and restoring operational services (grouped together by NIST because they occur iteratively).
4. **Post-Incident Activity:** Post-mortem review, metrics compilation, and defensive updating.

### 2. SANS PICERL Model

![SANS PICERL Model](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/6b6b1992-ccf4-4be8-81c7-279bfc11e485/sans-ir-model-4c53baa12.webp)

The SANS Institute separates technical operations into six distinct, actionable stages:
1. **P - Preparation:** Policy formulation, incident playbooks, telemetry enablement, and forensic readiness.
2. **I - Identification (Detection & Analysis):** Validating IOCs, scoping compromised hosts, and triaging alerts.
3. **C - Containment:** Short-term isolation and long-term network segmentation to isolate infected systems.
4. **E - Eradication:** Removing backdoors, persistent malware, and remediating root cause flaws.
5. **R - Recovery:** Validated restoration of operational systems, data rollback, and enhanced monitoring.
6. **L - Lessons Learned:** Executive reporting, tracking KPIs, and feeding insights back into Preparation.

### Framework Comparison

| NIST SP 800-61 Phase | SANS PICERL Phase | Operational Focus & Primary Objectives |
| :--- | :--- | :--- |
| **Preparation** | **Preparation** | Team readiness, call trees, SIEM logging architecture, baseline policies. |
| **Detection & Analysis** | **Identification** | Confirming genuine compromise, assessing scope, triaging severity. |
| **Containment, Eradication, & Recovery** | **Containment** | Quarantining affected hosts, blocking C2 egress, severing attacker access. |
| **Containment, Eradication, & Recovery** | **Eradication** | Purging persistence hooks, cleaning web shells, patching exploited CVEs. |
| **Containment, Eradication, & Recovery** | **Recovery** | Restoring from pre-breach backups, gradual service enablement, live telemetry monitoring. |
| **Post-Incident Activity** | **Lessons Learned** | Conducting post-mortems, evaluating MTTD/MTTR metrics, updating documentation. |

> [!TIP] Framework Application in Practice
> In enterprise environments, security teams frequently use **SANS PICERL** for tactical runbooks and technical playbook workflows due to its discrete separation of containment, eradication, and recovery. In contrast, **NIST SP 800-61** is the preferred standard for audit compliance, executive reporting, and formal government governance.

### The "Golden Hour" in Incident Response

Analogous to emergency trauma medicine, the **Golden Hour** represents the critical window immediately following breach detection. Rapid, deliberate action during this initial window limits attacker lateral movement, captures volatile forensic memory, and prevents adversaries from wiping logs or deploying anti-forensics tools.

---

## Phase 1: Preparation

Preparation is the proactive foundation of incident response. Discovering that endpoint logs are disabled or backups are corrupt during an active intrusion turns a manageable containment into a catastrophe.

Preparation spans two interconnected domains:

### 1. People & Process Preparation

- **Incident Response Plan (IRP):** Formally documented organizational policy detailing escalation thresholds, decision-making authority (e.g., who has authority to sever internet connectivity for production data centers), and operational roles.
- **Call Trees & Out-of-Band Communications:** Verified, redundant contact lists for technical responders, executive leadership (CISO, CTO, General Counsel), and external incident retainers. Response teams must maintain out-of-band communication channels (e.g., dedicated Signal groups or off-network chat instances) in case corporate email or Microsoft Teams is compromised.
- **Tabletop Exercises (TTX):** Regular simulation exercises walking stakeholders through realistic crisis scenarios (e.g., executive phishing leading to widespread ransomware encryption).

### 2. Technology & Data Readiness

- **Centralized Log Visibility (SIEM):** Aggregating and time-synchronizing logs (NTP) from:
  - *Endpoints:* Advanced telemetry tools such as **Microsoft Sysmon** (System Monitor) to log process ancestry, network connections, and PowerShell Script Block Logging (Event ID 4104).
  - *Network Infrastructure:* Perimeter firewall logs, DNS queries, web proxies, and NetFlow.
  - *Applications:* Web server access/error logs (Nginx, Apache, IIS) and database audit trails.
- **System Baselining:** Establishing behavioral norms across the enterprise. Without a known baseline, detecting anomalies is nearly impossible.
  - What are the regular network communication patterns (destinations, data volume, off-hours traffic)?
  - What legitimate service accounts run on key enterprise servers?
- **Asset Inventory & Crown Jewels Identification:** Maintaining an updated inventory prioritizing mission-critical assets (Active Directory Domain Controllers, customer databases, intellectual property repositories).
- **Forensic Toolkit (Jump Bag):** A dedicated, sanitized digital and physical response kit:
  - Clean, dedicated forensic workstations disconnected from corporate production subnets.
  - Bootable forensic Linux environments (e.g., SIFT Workstation, CAINE).
  - Validated forensic tools: FTK Imager, KAPE, Volatility, Eric Zimmerman Tools.
  - Offline, air-gapped backups and configuration files.

### Preparation Phase Checklist

- [ ] Written Incident Response Plan approved by executive leadership.
- [ ] Documented and tested escalation call tree.
- [ ] Centralized SIEM ingesting endpoint (Sysmon), network, and proxy logs.
- [ ] Network baselines established and asset classification documented.
- [ ] Offline, immutable backups maintained and regularly tested for restoration.
- [ ] Specialized forensic analysis tools maintained and ready for immediate deployment.

---

## Phase 2: Identification (Detection & Analysis)

The Identification phase focuses on validating alerts, scoping the intrusion, and triaging severity to ensure appropriate resource allocation.

### Detection Channels

1. **Automated Alerts:**
   - **SIEM Correlation Rules:** Multi-event correlation (e.g., 20 failed login attempts followed by an immediate successful login from an anomalous geolocation).
   - **EDR Telemetry:** Flags unauthorized memory injection, process hollowings, or credential dumping attempts.
   - **NIDS / NIPS:** Signature and protocol anomaly detections across network boundaries.
2. **Manual & Proactive Reporting:**
   - **End-User Reports:** Employees flagging suspicious authentication prompts, sudden performance drops, or rogue pop-ups.
   - **Threat Hunting:** Proactive queries looking for persistence artifacts or abnormal network beacons.
   - **External Notifications:** Intelligence notifications from law enforcement, ISPs, or threat intelligence platforms indicating corporate credentials or assets have been compromised.

### Detection Paradigms: Signatures vs. Behavioral Anomalies

```text
Detection Approaches:
  ├── Signature-Based Detection
  │     ├── Matches known byte sequences or cryptographic file hashes (MD5, SHA-256).
  │     ├── Pros: Extremely low false-positive rate for known malware.
  │     └── Cons: Completely blind to novel zero-days, recompiled binaries, and fileless LOLBins.
  └── Anomaly & Behavioral Detection
        ├── Flags deviations from historical baselines (e.g., winword.exe spawning powershell.exe).
        ├── Pros: Highly effective at detecting zero-days and living-off-the-land techniques.
        └── Cons: Susceptible to false positives caused by unusual but benign operations.
```

### Triage & Scope Determination

Upon receiving an alert, analysts must validate the incident before initiating widespread containment:
1. **True Positive vs. False Positive:** Does the alert represent a legitimate threat or benign activity (e.g., an internal vulnerability scan triggering IDS port scan alerts)?
2. **Blast Radius & Scope:** How many systems, user identities, and network segments are involved?
3. **Impact Assessment:** Does the intrusion threaten confidentiality, data integrity, or core service availability?

### Incident Severity Tiers

- **Low:** Contained commodity adware, single-user phishing attempts blocked by filters, isolated port scans.
- **Medium:** Policy violations, malware executed on non-critical endpoints without lateral movement.
- **High:** Privilege escalation on domain members, compromised privileged user credentials, active data exfiltration.
- **Critical:** Compromise of Active Directory Domain Controllers, active ransomware encrypting virtualized infrastructure, massive PII/regulatory data exposure.

---

## Technical Investigation Methodologies

### 1. Web Attack Log Analysis

Adversaries targeting public web servers leave distinctive indicators across HTTP access logs.

#### Scenario A: SQL Injection (SQLi)
*Normal Baseline Request:*
```http
GET /search.php?q=iphone HTTP/1.1
Host: vulnerable-shop.com
User-Agent: Mozilla/5.0...
```
*Response:* `HTTP 200 OK` (Standard catalogue search results returned).

*Malicious Request (SQL Injection):*
```http
GET /search.php?q=iphone' UNION SELECT 1,@@version-- HTTP/1.1
Host: vulnerable-shop.com
User-Agent: Mozilla/5.0...
```
*Technical Breakdown:*
- `'` (Single quote): Closes the string literal within the backend SQL query.
- `UNION SELECT`: Concatenates attacker-crafted results with the original query.
- `@@version`: Database environmental variable returning system version details.
- `--`: SQL comment token instructing the parser to ignore the remainder of the query.
- *Analyst Verification:* If the response returns `HTTP 200 OK` with database metadata (e.g., `5.7.33-log`) or an abnormal `Content-Length`, the application is confirmed vulnerable.

#### Scenario B: OS Command Injection
*Exploit Request:*
```http
POST /ping.php HTTP/1.1
Host: target-server.com
Content-Type: application/x-www-form-urlencoded

ip=127.0.0.1; cat /etc/passwd
```
*Technical Breakdown:* The semicolon (`;`) serves as a command terminator in Unix-like shells, forcing the underlying operating system to evaluate `cat /etc/passwd` immediately following the ping utility. If response headers or body display root user entries (`root:x:0:0:...`), command execution has succeeded.

### 2. Windows Endpoint Forensics

When investigating an endpoint suspected of compromise, analysts inspect key volatile and persistent artifacts:

- **Process Lineage Anomaly Detection:** Using tools like Process Explorer or Process Hacker:
  - Legitimate processes running from anomalous directories (e.g., `svchost.exe` executing out of `C:\Users\Admin\AppData\Local\Temp` instead of `C:\Windows\System32`).
  - Illegitimate parent processes (e.g., `lsass.exe` spawned by an arbitrary user process rather than `winlogon.exe`).
  - Disguised filenames masquerading as core system binaries (`scvhost.exe`, `chorme.exe`, `svhost.exe`).
- **Persistence Verification:**
  - *Registry Run Keys:* Inspecting `HKCU` and `HKLM` hives under `\Software\Microsoft\Windows\CurrentVersion\Run`.
  - *Scheduled Tasks:* Querying scheduled jobs from the command prompt:
    ```cmd
    schtasks /query /fo LIST /v
    ```
  - *Startup Folders:* Examining `%AppData%\Microsoft\Windows\Start Menu\Programs\Startup`.
- **PowerShell Telemetry:** Inspecting Windows Event Viewer under Event ID `4104` (Script Block Logging) to analyze deobfuscated PowerShell commands executed in memory, including Base64-encoded strings (`-EncodedCommand`).

### 3. Memory (RAM) Forensics

Volatile system memory preserves active processes, decrypted network sockets, injected DLLs, and unsaved command history. Using memory analysis frameworks such as **Volatility**:
- Extracting injected or unlinked process structures.
- Inspecting active and terminated network sockets (equivalent to historical `netstat` data).
- Reconstructing command-line arguments and bash/cmd execution histories.
- Extracting plaintext credentials or cryptographic keys held in memory.

### Incident Analysis Toolset

- **Network Forensics:** Wireshark, Zeek, tcpdump.
- **Host & System Analysis:** Sysinternals Suite (Autoruns, Process Explorer), KAPE, FTK Imager.
- **Memory Forensics:** Volatility Framework, Rekall.
- **Data Decoding & Deobfuscation:** CyberChef.

---

## Phase 3: Containment Strategies

Once an attack is validated, the priority shifts to preventing lateral movement, stopping data exfiltration, and limiting overall damage.

```text
Containment Paradigms:
  ├── Short-Term Containment (Immediate Damage Limitation)
  │     ├── Endpoint network isolation (severing physical or virtual NICs).
  │     ├── Perimeter firewall IP/domain blocking for active C2 servers.
  │     └── Immediate revocation and lockout of compromised user accounts.
  └── Long-Term Containment (Sustainable Operational Isolation)
        ├── Implementing WAF rules or temporary virtual patches for exploited CVEs.
        ├── Isolating affected VLAN subnets via access-control lists (ACLs).
        └── Forcing enterprise-wide password resets (including the Active Directory krbtgt account twice).
```

### The "Pull the Plug" Dilemma: Power Down vs. Network Isolation

A common beginner mistake is immediately powering down or pulling the power cord on a compromised server.

> [!CAUTION] Never Power Down Indiscriminately
> Powering off a machine wipes volatile memory (RAM), destroying active network sockets, injected memory payloads, running processes, and encryption keys. Furthermore, sophisticated malware may employ a **Dead Man's Switch**, triggering destructive disk wiper routines if the process loses power or connectivity.

**Standard Operating Procedure:**
1. If immediate data destruction (e.g., high-speed ransomware encryption) is actively occurring, pulling the power immediately may save unencrypted disk blocks.
2. In all other scenarios, keep the system powered on, capture a volatile memory (RAM) image, and sever network connectivity (unplug the Ethernet cable or disconnect the virtual NIC via hypervisor controls).

### Chain of Custody & Evidence Preservation

Any evidence that may be introduced in legal proceedings must adhere to strict **Chain of Custody** standards. Investigators must generate forensic disk images (bit-stream copies) using write-blocking hardware and immediately compute cryptographic hashes (SHA-256) of the source disk and target image. Any hash mismatch invalidates the evidence in court.

---

## Phase 4: Eradication

Eradication focuses on completely purging malicious artifacts from the environment and eliminating the root cause vulnerabilities that enabled the initial compromise.

### Core Eradication Objectives

1. **Remediating the Root Cause:**
   - Patching the vulnerable service or application that allowed remote exploitation.
   - Enforcing Multi-Factor Authentication (MFA) and strengthening password complexity if credentials were brute-forced.
   - Updating Email Gateway content filters if initial access occurred via spear-phishing.
2. **Comprehensive Malware & Persistence Removal:**
   - Terminating malicious running processes and deleting injected binaries.
   - Removing unauthorized registry run entries, cron jobs, scheduled tasks, and local user backdoors.
   - Scanning storage with updated enterprise EDR tools.

### Re-Imaging vs. System Disinfection

> [!IMPORTANT] The Golden Rule of System Integrity
> *"Once a system has been fully compromised with administrative privileges, it can never be trusted 100%."*

Sophisticated adversaries deploy rootkits, UEFI/firmware implants, and obfuscated backdoors that evade standard antivirus scans. The standard enterprise approach is **re-imaging the system from a verified, clean "Golden Image"** rather than attempting piecemeal file cleanup.

**Best Practice Workflow:**
1. Back up non-executable user data files only (never executables or system configurations).
2. Format the storage drives.
3. Deploy a fresh operating system installation from an authenticated, patched base image.
4. Restore clean data files following thorough malware scanning.

### Credential Revocation & Enterprise Hardening

Adversaries often harvest memory credentials (via LSASS dumping) during dwell time. Eradication requires:
- Forcing password resets for all affected domain users.
- Cycling database connection strings and service account passwords.
- Revoking and regenerating SSH keys, TLS certificates, and API tokens.
- Resetting the Active Directory `krbtgt` account password twice (with an interval between resets to prevent Kerberos ticket invalidation outages) if domain controller compromise is suspected.

---

## Phase 5: Recovery

Recovery is the structured process of returning restored systems to production while ensuring they remain resilient against re-infection.

### 1. Restoration & System Validation
- **Backup Integrity Verification:** Ensure restore points precede the initial breach timestamp to avoid re-introducing dormant malware backdoors.
- **Vulnerability Assessment:** Conduct comprehensive vulnerability scans on restored hosts prior to placing them back on production subnets.
- **Service Validation:** Verify data integrity, application performance, and database connectivity.

### 2. Phased Restoration Strategy
Avoid reconnecting all systems simultaneously. Follow a tiered recovery sequence:
1. Core identity and network infrastructure (DNS, Active Directory Domain Controllers).
2. Core backend database and application servers.
3. Front-end web portals and external communication channels.
4. General user endpoints and workstations.

### 3. Heightened Telemetry & Monitoring
Following recovery, systems remain under enhanced surveillance:
- **Verbose Logging:** Enable temporary debug/verbose audit levels on affected servers.
- **Lower Alert Thresholds:** Tune SIEM rules to trigger on lower anomaly thresholds (e.g., alert on three failed logins rather than ten).
- **Custom IOC Rules:** Deploy custom IDS and EDR detection rules specifically matching hashes, filenames, and network indicators observed during the incident.

### Declaring Recovery Complete
Recovery is formally closed when the CSIRT leader and operational stakeholders confirm:
- Systems are fully patched and functioning normally without operational disruption.
- Continuous monitoring reveals zero reinfection attempts or anomalous beaconing for an established quiet period (typically 48 to 72 hours).

---

## Phase 6: Lessons Learned (Post-Incident Activity)

The final phase transforms an adverse security event into tangible organizational improvements.

### 1. The Post-Mortem Review

Conducted within two weeks of incident closure, the post-mortem brings together all stakeholders (technical responders, managers, legal counsel). The meeting must focus on constructive analysis rather than personal blame:
- What was the complete chronology of the intrusion?
- What was the **Mean Time to Detect (MTTD)** and **Mean Time to Respond (MTTR)**?
- Which operational procedures and detection signatures functioned effectively?
- Where were visibility gaps or tooling limitations encountered?
- Did communications between teams proceed smoothly, or did bottlenecks emerge?

### 2. Formal Incident Response Report Template

```markdown
# Incident Response Report

**Incident ID:** IR-2023-001  
**Date:** October 25, 2023  
**Lead Investigator:** CSIRT Lead Analyst  
**Classification:** Ransomware / Data Exfiltration Attempt  
**Severity:** Critical  

## 1. Executive Summary
On October 23, 2023, at 09:15 UTC, the Security Operations Center identified unauthorized file modification activity on a Finance departmental file server. Initial compromise originated from a spear-phishing attachment opened by an internal user. The CSIRT isolated the host within 45 minutes of initial alert generation. No business records were exfiltrated or permanently lost; the system was reconstructed from verified offline backups and returned to production within 6 hours.

## 2. Comprehensive Incident Timeline
- **Oct 23, 08:30 UTC:** Adversary transmitted an email containing a malicious macro-enabled workbook to accounting@company.com.
- **Oct 23, 08:45 UTC:** Recipient opened attachment; malicious VBA macro spawned PowerShell process.
- **Oct 23, 09:00 UTC:** PowerShell established outbound connection to C2 IP (192.0.2.55), retrieving secondary stage payload.
- **Oct 23, 09:15 UTC:** SIEM triggered high-priority alert: "Rapid File Renaming Anomaly".
- **Oct 23, 09:30 UTC:** CSIRT validated true positive and enacted network isolation of target host.
- **Oct 23, 11:30 UTC:** Eradication completed; persistence mechanisms documented and C2 indicators blocked enterprise-wide.
- **Oct 23, 15:30 UTC:** Clean restoration from verified offline backup completed; server returned to production.

## 3. Technical Analysis & Root Cause
Initial access was facilitated by macro execution allowed by local workstation settings. The endpoint antivirus failed to intercept the payload due to polymorphic packing.
- **Indicators of Compromise (IOCs):**
  - SHA-256 Hash: `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855`
  - C2 IP Address: `192.0.2.55`
  - Exploit Domain: `cdn-update-service.com`

## 4. Remediation Actions Executed
- Re-imaged compromised workstation from base golden image.
- Revoked and regenerated user credentials and related service tokens.
- Deployed global firewall and DNS block rules for identified C2 endpoints.

## 5. Strategic Recommendations & Defensive Improvements
- [ ] Implement Group Policy Objects (GPO) to globally prohibit Office macro execution from untrusted internet files.
- [ ] Enhance Email Gateway inspection rules to sandbox inbound macro-enabled attachments.
- [ ] Schedule mandatory interactive phishing awareness training for administrative personnel.
```

### Key IR Operational Metrics
- **Mean Time to Detect (MTTD):** Elapsed time between initial compromise and detection.
- **Mean Time to Acknowledge (MTTA):** Time between alert generation and triage initiation.
- **Mean Time to Contain (MTTC):** Time required to isolate compromised systems.
- **Mean Time to Recover (MTTR):** Time to restore business services to normal baseline.

---

## Incident Response Tactical Playbooks

Playbooks provide deterministic, step-by-step procedures for handling common incident categories.

### Playbook 1: Phishing Investigation Workflow

```text
[Phishing Alert Received] 
        │
        ├──> 1. Header Analysis
        │       ├── Compare 'From' vs. 'Return-Path'
        │       └── Verify SPF, DKIM, and DMARC states (PASS / FAIL)
        │
        ├──> 2. Body & Hyperlink Inspection
        │       ├── Defang URLs (e.g., hxxps[://]example[.]com)
        │       └── Analyze destination via URLScan.io or VirusTotal
        │
        ├──> 3. Attachment Sandboxing
        │       ├── Compute cryptographic hash (SHA-256)
        │       └── Detonate inside isolated sandbox (Cuckoo / Joe Sandbox)
        │
        └──> 4. Containment & Remediation
                ├── Purge message enterprise-wide from mailboxes
                ├── Blacklist sending domain, IP, and URLs
                └── Identify and remediate users who clicked or submitted credentials
```

### Playbook 2: Ransomware Containment Workflow

1. **Trigger:** User reports extortion screen, files append encrypted extensions (`.locked`, `.enc`), or SIEM alerts on rapid mass file modifications.
2. **Containment (Urgent):**
   - Sever network access on affected endpoints immediately (disconnect Ethernet cables, disable Wi-Fi, sever virtual NICs). **Do not shut down the computer** unless disk encryption is actively proceeding in real time.
   - Restrict shared network storage and place critical file shares into read-only mode to prevent propagation.
   - Isolate the originating network subnet at the switch/firewall layer.
3. **Forensic Analysis:**
   - Upload sample encrypted files and ransom notes to identification repositories (e.g., **ID Ransomware**).
   - Check the **No More Ransom** project for publicly available decryptor utilities.
   - Trace initial intrusion vectors (exposed RDP ports, phishing emails, unpatched edge vulnerabilities).
4. **Eradication & Recovery:**
   - Wipe storage drives completely.
   - Restore data from verified, offline backups dated prior to the intrusion.
   - Validate and patch all vulnerabilities prior to rejoining systems to production.

---

## Legal, Regulatory, and Crisis Communication

Incident handling extends beyond technical containment into statutory compliance and public relations management.

### Chain of Custody Documentation

Forensic evidence must be documented meticulously to maintain legal admissibility:

| Form Field | Description & Requirement |
| :--- | :--- |
| **Case Reference ID** | Unique internal tracking identifier (e.g., `IR-2023-001`). |
| **Item Description** | Hardware make, model, capacity, serial numbers, and physical condition. |
| **Cryptographic Hash** | SHA-256 hash calculated immediately upon forensic acquisition. |
| **Timestamp** | Date, exact time (UTC), and physical location of acquisition. |
| **Custody Transfers** | Signatures, names, and explicit transfer reasons for every handoff. |

### Regulatory Disclosure Obligations

Data protection laws enforce strict reporting timelines when personal identifiable information (PII) is compromised:
- **GDPR (General Data Protection Regulation):** Mandates formal breach notification to relevant supervisory authorities within **72 hours** of becoming aware of the incident.
- **US State & Federal Standards (e.g., SEC, HIPAA):** Enforce strict notification timelines and financial penalties depending on data sensitivity and affected individual counts.
- **Legal Coordination:** The CSIRT must work closely with General Counsel to determine whether breached data triggers statutory disclosure requirements.

### Crisis Communication Principles

- **Internal Communications:** Apply the principle of need-to-know. Distribute verified facts to technical and operational personnel while avoiding premature speculation that fuels company-wide panic.
- **External Communications:** Designate a single trained corporate spokesperson. Never issue definitive statements regarding data loss ("No customer records were stolen") before forensic investigations confirm those assertions.
